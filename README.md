## Try/Catch Example

````sql

BEGIN TRY
    BEGIN TRAN		

    COMMIT TRAN
END TRY
BEGIN CATCH
    --SELECT -- stuff for debug logging...
    --	 ERROR_NUMBER() AS ErrorNumber
    --	,ERROR_SEVERITY() AS ErrorSeverity
    --	,ERROR_STATE() AS ErrorState
    --	,ERROR_PROCEDURE() AS ErrorProcedure
    --	,ERROR_LINE() AS ErrorLine
    --	,ERROR_MESSAGE() AS ErrorMessage;
    IF(@@TRANCOUNT > 0) BEGIN
        ROLLBACK TRAN

	-- log stuff
    END

END CATCH

````

## FLOOR A DATE

````sql

declare @datetime datetime;
set @datetime = getdate();
select @datetime;
select dateadd(year,datediff(year,0,@datetime),0);
select dateadd(month,datediff(month,0,@datetime),0);
select dateadd(day,datediff(day,0,@datetime),0);
select dateadd(hour,datediff(hour,0,@datetime),0);
select dateadd(minute,datediff(minute,0,@datetime),0);
select dateadd(second,datediff(second,'2000-01-01',@datetime),'2000-01-01');

````

## DATETIME TO STRING

Here's a good link for this stuff: http://www.sql-server-helper.com/tips/date-formats.aspx

## UPDATE ROW AND RETURN COLUMN VALUE

Use the 'OUTPUT' sql variable to do this.  More info at: http://stackoverflow.com/questions/700786/sql-update-a-row-and-returning-a-column-value-with-1-query

## An example of calling a stored proc and retrieving the output result set

````csharp

try
{
    using (var sqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["xxxx"].ConnectionString))
    {
        using (var sqlCommand = new SqlCommand("[dbo].[xxxxx]", sqlConnection))
        {
            sqlCommand.CommandType = CommandType.StoredProcedure;
            sqlCommand.Parameters.AddWithValue("@Id", id);
            sqlConnection.Open();

            using (var sqlDataReader = sqlCommand.ExecuteReader())
            {
                while (sqlDataReader.HasRows && sqlDataReader.Read())
                {
                    someColumn = sqlDataReader["xxxxxx"].ToString();
                }
            }
        }
    }
}
catch (Exception ex)
{
    throw;
}

````

## An example of calling a stored proc with output parameters and nullable parameters

````csharp

try
{
    using (var sqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["xxxx"].ConnectionString))
    {
        using (var sqlCommand = new SqlCommand("[dbo].[xxxxxxxx]", sqlConnection))
        {
            sqlCommand.CommandType = CommandType.StoredProcedure;

            sqlCommand.Parameters.AddWithValue("@Id", id);
            sqlCommand.Parameters.AddWithValue_Nullable("@Optional", optional);
            sqlCommand.Parameters.AddWithValue("@returnCode", returnCode).Direction = ParameterDirection.Output;

            sqlConnection.Open();
            sqlCommand.ExecuteNonQuery();

            returnCode = Convert.ToInt32(sqlCommand.Parameters["@returnCode"].Value);
        }
    }
}
catch (Exception ex)
{
    throw;
}

````

## An example of calling a function

````csharp

try
{
    using (var sqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["xxxxx"].ConnectionString))
    {
        using (var sqlCommand = new SqlCommand(@"[dbo].[xxxxxx]", sqlConnection))
        {
            sqlCommand.CommandType = CommandType.StoredProcedure;

            var inputParam = new SqlParameter()
            {
                ParameterName = "@Id",
                SqlDbType = SqlDbType.NVarChar,
                Direction = ParameterDirection.Input,
                Value = id
            };

            var output = new SqlParameter()
            {
                ParameterName = "@Result",
                SqlDbType = SqlDbType.NVarChar,
                Direction = ParameterDirection.ReturnValue
            };

            sqlCommand.Parameters.Add(inputParam);
            sqlCommand.Parameters.Add(output);

            sqlConnection.Open();
            sqlCommand.ExecuteNonQuery();

            if (output.Value != DBNull.Value)
            {
                result = (string)output.Value;
            }

        }
    }
}
catch (Exception ex)
{
    throw;
}

````

## An example of calling a SQL query

````csharp

try
{
    using (var sqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["xxxxx"].ConnectionString))
    {
        using (var sqlCommand = new SqlCommand("INSERT INTO [dbo].[xxxx] ( [Id] ,[Stuff] ) VALUES ( @Id, convert(nvarchar(500), @stuff))", sqlConnection))
        {
            sqlCommand.CommandType = CommandType.Text;

            sqlCommand.Parameters.AddWithValue_Nullable("@Id", id);
            sqlCommand.Parameters.AddWithValue("@Stuff", stuff);

            sqlConnection.Open();
            sqlCommand.ExecuteNonQuery();
        }
    }
}
catch (Exception ex)
{
    throw;
}

````

## An example of calling a SQL query and getting data back

````csharp

try
{
    using (var sqlConnection = new SqlConnection(ConfigurationManager.ConnectionStrings["xxxx"].ConnectionString))
    {
        using (var sqlCommand = new SqlCommand("SELECT COUNT([Id]) FROM [dbo].[xxxxx] WHERE [Stuff] = 1", sqlConnection))
        {
            sqlCommand.CommandType = CommandType.Text;                        
            sqlConnection.Open();
            using (var sqlDataReader = sqlCommand.ExecuteReader())
            {
                while (sqlDataReader.Read())
                {
                    result = Convert.ToInt32(sqlDataReader[0]) == 0;
                }
            }
        }
    }
}
catch (Exception ex)
{
    throw;
}

````
