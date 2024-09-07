# How to Delete and existing Record
## Step 1: Create a SP for Delete
```sql
CREATE PROCEDURE [dbo].[PR_State_DeleteByPK]
    @StateID INT
AS
BEGIN
    DELETE FROM [dbo].[State]
    WHERE [dbo].[State].[StateID] = @StateID
END
```

## Step 2: Add Delete event in Repeater control in view page
Here we will add Delete button in StateList.aspx page to Delete a record
```csharp
<table class="table">
    <asp:Repeater runat="server" ID="rptStateList" OnItemCommand="rptStateList_ItemCommand">
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
                <td>
                    <asp:Button runat="server" ID="btnEdit" CssClass="btn btn-primary" Text="Edit"  CommandName="Edit" CommandArgument='<%# Eval("StateID") %>'/>
                    <asp:Button runat="server" ID="btnDelete"  CssClass="btn btn-danger"  Text="Delete" CommandName="Delete" CommandArgument='<%# Eval("StateID") %>'/>
                </td>
            </tr>
        </ItemTemplate>
    </asp:Repeater>
</table>
```

## Step 3: Add Repeater event in class file
Once we create an event for repeater, it will generate the event in aspx.cs file add logic over there to delete a record.
```csharp
protected void rptStateList_ItemCommand(object source, RepeaterCommandEventArgs e)
{
    if (e.CommandName == "Delete")
    {
        try
        {
            SqlConnection obj = new SqlConnection(ConfigurationManager.ConnectionStrings["ABConnection"].ToString());
            obj.Open();
            SqlCommand objCmd = obj.CreateCommand();
            objCmd.CommandType = CommandType.StoredProcedure;
            objCmd.CommandText = "PR_State_DeleteByPK";
            objCmd.Parameters.AddWithValue("@StateID", e.CommandArgument);
            objCmd.ExecuteNonQuery();
        }
        catch (Exception ex)
        {
            lblMessage.Text = ex.Message.ToString();
        }
    }
}
```

