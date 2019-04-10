```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, transaction.transactionID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
```

```csharp
private List<Tool> tools;
        private List<string> locations = new List<string>();
        private List<string> categories = new List<string>();
        private float price;
        private string toolName;
        
        public MainPage()
        {
            InitializeComponent();
            IniMyStuff(); 
        }

        private void IniMyStuff()
        {
            //LUODAAN DATAGRID KAIKISSA SAATAVILLA OLEVISTA TYÖKALUISTA
            tools = DB.GetToolsFromMysql();
            dgTools.ItemsSource = tools;

            //LUODAAN SUODATIN COMBOBOXIT
            var location = tools.Select(t => t.ToolLocation).Distinct();
            locations.Add("All cities");
            foreach (var item in location)
            {
                locations.Add(item);
            }

            var category = tools.Select(c => c.ToolCategoryName).Distinct();
            categories.Add("All categories");
            foreach (var item in category)
            {
                categories.Add(item);
            }

            cbCityFilter.ItemsSource = locations;
            cbToolCategory.ItemsSource = categories;

            //ACTIVE USER IMAGE
            imgMainPageProfile.Source = Active.ImageSource;
            //imgMainPageProfile.Source = new BitmapImage(new Uri(@"https://student.labranet.jamk.fi/~M3156/iTool/images/" + Active.ImageFileName, UriKind.RelativeOrAbsolute));

            //ACTIVE USER NAME
            txtUsername.Text = $"{Active.FirstName} {Active.LastName}";
        }

        private void GoToProfile()
        {
            ProfileWindow profile = new ProfileWindow();
            profile.Show();
            this.Close();
        }
```