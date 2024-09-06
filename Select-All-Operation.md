# How to Retrieve Data from Database

## Step 1: Prepare Stored Procedure
- Here we have created stored procedure for state table
- Here also applied join operation on country table. For that we have required country table so make sure you have country table
```sql
CREATE PROC [dbo].[PR_State_SelectAll]
AS
SELECT
	 [dbo].[State].[StateID]
	,[dbo].[State].CountryID
	,[dbo].[Country].[CountryName]
	,[dbo].[State].[StateName]
	,[dbo].[State].[StateCode]
	,[dbo].[State].[CreationDate]
	,[dbo].[State].[ModificationDate]
FROM [dbo].[State]

INNER JOIN [dbo].[Country]
ON [dbo].[State].[CountryID] =[dbo].[Country].[CountryID]

ORDER BY [dbo].[Country].[CountryName],[dbo].[State].[StateName]
```

## Step2: Create a State List web form to display data using Repeater Control
Here we have created a state list page to display data
```csharp
<div class="container pt-4 col-6">
        <h3>State List</h3>
        <hr />
        <table class="table">
            <asp:Repeater runat="server" ID="rptStateList">
                <HeaderTemplate>
                    <tr>
                        <th scope="col">Country Name</th>
                        <th scope="col">State Name</th>
                        <th scope="col">State Code</th>
                        <th scope="col">Action</th>
                    </tr>
                </HeaderTemplate>
                <ItemTemplate>
                    <tr>
                        <td scope="row"><%# Eval("CountryName") %></td>
                        <td><%# Eval("StateName") %></td>
                        <td><%# Eval("StateCode") %></td>
                        <td></td>
                    </tr>
                </ItemTemplate>
            </asp:Repeater>
        </table>
    </div>
```
## Stpe 3: Create DB Connection & Retrieve Data 
Do write code in YourFileName.aspx.cs
```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!Page.IsPostBack)
    {
        getStateData();
    }
}

private void getStateData()
{
    SqlConnection objConn = new SqlConnection("Data Source=YourSQLServerName; Initial Catalog=YourDBName; Integrated Security=true;");
    obj.Open();
    //2. Create Commond

    SqlCommand objCmd = obj.CreateCommand();
    objCmd.CommandType = CommandType.StoredProcedure;
    objCmd.CommandText = "PR_State_SelectAll";
    //3. Read Data
    SqlDataReader sdr = objCmd.ExecuteReader();
    DataTable dt = new DataTable();
    dt.Load(sdr);
    obj.Close();
    rptStateList.DataSource = dt;
    rptStateList.DataBind();
}
```
