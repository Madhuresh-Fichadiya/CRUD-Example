# How Add a new record and Update an existing record
## Step 1: Prepare a stored procedure
Create a SP for Insert and Update operation
Insert Operation
```sql
CREATE PROC [dbo].[PR_State_Insert]
@StateName varchar(100),
@StateCode int,
@CountryID int,
@ModificationDate datetime
AS
BEGIN
-- Actual Query

	INSERT INTO [dbo].[State]
	(
		 StateName
		,StateCode
		,CountryID
		,ModificationDate
	)

	VALUES
	(
		  @StateName
		, @StateCode
		, @CountryID
		, @ModificationDate
	)
END
```
Update procedure
```sql
CREATE PROC [dbo].[PR_State_UpdateByPK]
@StateID int,
@StateName varchar(100),
@StateCode int,
@CountryID int,
@ModificationDate datetime
AS
BEGIN
UPDATE [dbo].[State]
	SET 
		 StateName = @StateName
		,StateCode = @StateCode
		,CountryID = @CountryID
		,ModificationDate = @ModificationDate
	
	WHERE [dbo].[State].[StateID] = @StateID
END
```
## Step 2: Design a view page
Design a view page as per requirement
```csharp
<div class="container pt-4 col-6">
    <h3>State Add/Edit </h3>
    <hr />
    <div class="form-row">
        <asp:Label runat="server" ID="lblMessage"></asp:Label>
            <div class="form-row">
                <label><span class="text-danger">*</span>Select Country</label>
                <asp:DropDownList runat="server" ID="ddlCountry" class="form-control">
                </asp:DropDownList>
            </div>
            <div class="form-group">
                <label><span class="text-danger">*</span>State Name</label>
                <asp:TextBox runat="server" ID="txtStateName" class="form-control"></asp:TextBox>
            </div>
            <div class="form-group">
                <label for="inputCity"><span class="text-danger">*</span>State Code</label>
                <asp:TextBox runat="server" ID="txtStateCode" class="form-control"></asp:TextBox>
            </div>
            <br />
            <div class="form-group">
                <asp:Button runat="server" ID="btnSave" Text="Save" class="btn btn-success" OnClick="btnSave_Click" />
                <asp:HyperLink runat="server" ID="hlCancel" Text="Cancel" NavigateUrl="~/State/StateList.aspx" CssClass="btn btn-danger"></asp:HyperLink>
            </div>
    </div>
</div>
```
## Step 3: Add Save event in class file
Do write logic for insert update in save click event
```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!Page.IsPostBack)
    {
        FillCountryDropDown();
    }
}
protected void btnSave_Click(object sender, EventArgs e)
{
    try
    {
        string StateName = String.Empty;
        int StateCode;
        int CountryID;
        DateTime ModificationDate = DateTime.Now;

        StateName = txtStateName.Text.Trim();
        StateCode = Convert.ToInt32(txtStateCode.Text.Trim());
        CountryID = Convert.ToInt32(ddlCountry.SelectedValue);

        //Step 1: Create DB Connection
        SqlConnection objConn = new SqlConnection("Data Source=DESKTOP-SJ5GNSA; Initial Catalog=AddressBookDB; Integrated Security=true;");
        objConn.Open();

        //2. Create Commond and Pass parameteres to SP
        SqlCommand objCmd = objConn.CreateCommand();
        objCmd.CommandType = CommandType.StoredProcedure;
        objCmd.CommandText = "PR_State_Insert";
        objCmd.Parameters.AddWithValue("@StateName",StateName);
        objCmd.Parameters.AddWithValue("@CountryID",CountryID);
        objCmd.Parameters.AddWithValue("@StateCode",StateCode);
        objCmd.Parameters.AddWithValue("@ModificationDate",ModificationDate);

        //3. Insert Data
        if (objCmd.ExecuteNonQuery() > 0)
        {
            lblMessage.Text = "Record Inserted";
        }
        objConn.Close();
    }
    catch (Exception ex)
    {
        lblMessage.Text = ex.Message;
    }
}

private void FillCountryDropDown()
{
    //Step 1: Create DB Connection
    SqlConnection objConn = new SqlConnection("Data Source=DESKTOP-SJ5GNSA; Initial Catalog=AddressBookDB; Integrated Security=true;");

    objConn.Open();
    //2. Create Commond

    SqlCommand objCmd = objConn.CreateCommand();
    objCmd.CommandType = CommandType.Text;
    objCmd.CommandText = "SELECT CountryID, CountryName FROM Country";

    //3. Read Data
    SqlDataReader sdr = objCmd.ExecuteReader();
    DataTable dt = new DataTable();
    dt.Load(sdr);
    objConn.Close();

    //4. Bind Data to Source
    ddlCountry.DataSource = dt;
    ddlCountry.DataTextField = "CountryName";
    ddlCountry.DataValueField = "CountryID";
    ddlCountry.DataBind();
    ddlCountry.Items.Insert(0, new ListItem("--- Select Country ---", "-99"));        
}

private void ClearControls()
{
    txtStateName.Text = String.Empty;
    txtStateCode.Text = String.Empty;
}
```
