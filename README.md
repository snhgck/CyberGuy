using System;
using System.Data;
using System.Data.SqlClient;

public class PermTimelineDataPuller
{
    private readonly string _connectionString;

    public PermTimelineDataPuller(string connectionString)
    {
        _connectionString = connectionString;
    }

    public DataTable GetPermTimelineData(DateTime startDate, DateTime endDate)
    {
        using (SqlConnection connection = new SqlConnection(_connectionString))
        {
            connection.Open();
            
            string query = @"
                SELECT *
                FROM PermTimeline
                WHERE TimeStamp BETWEEN @StartDate AND @EndDate
                ORDER BY TimeStamp";

            using (SqlCommand command = new SqlCommand(query, connection))
            {
                command.Parameters.AddWithValue("@StartDate", startDate);
                command.Parameters.AddWithValue("@EndDate", endDate);

                DataTable dataTable = new DataTable();
                using (SqlDataAdapter adapter = new SqlDataAdapter(command))
                {
                    adapter.Fill(dataTable);
                }
                
                return dataTable;
            }
        }
    }
}

// Example usage:
public class Program
{
    static void Main()
    {
        string connectionString = "Server=YourServer;Database=YourDatabase;Trusted_Connection=True;";
        var dataPuller = new PermTimelineDataPuller(connectionString);

        DateTime startDate = DateTime.Now.AddDays(-30); // Last 30 days
        DateTime endDate = DateTime.Now;

        DataTable result = dataPuller.GetPermTimelineData(startDate, endDate);

        // Process the data
        foreach (DataRow row in result.Rows)
        {
            Console.WriteLine($"TimeStamp: {row["TimeStamp"]}, Value: {row["Value"]}");
        }
    }
}