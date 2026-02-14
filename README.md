# Telegram Expense Tracker with Automated Financial Reporting

A smart personal finance management system that lets you track expenses through Telegram and automatically logs them to Google Sheets with AI-powered parsing. Never miss an expense - just message your bot, and AI handles categorization, formatting, and storage. Includes automated monthly financial reports delivered right to your Telegram.

## 📊 Overview

This automation creates a complete expense tracking solution that:
- Accepts natural language expense messages via Telegram
- Uses AI (Google Gemini) to parse and categorize expenses
- Automatically logs to Google Sheets with proper formatting
- Supports single and multiple expenses in one message
- Generates monthly financial reports automatically
- Provides instant confirmation messages
- Handles Nigerian Naira (₦) currency formatting

## ✨ Key Features

- **Natural Language Input**: "I spent 5000 on food" → Automatically parsed and logged
- **AI-Powered Categorization**: Smart category assignment (Food, Transport, Shopping, etc.)
- **Multi-Expense Support**: Log multiple expenses in one message
- **Google Sheets Integration**: All data synced to spreadsheet in real-time
- **Conversation Memory**: AI remembers context within conversation
- **Monthly Reports**: Automated financial summaries with charts and insights
- **Instant Confirmation**: Get immediate feedback when expenses are logged
- **Currency Formatting**: Automatic ₦ (Naira) symbol and thousands separator
- **Date Intelligence**: AI understands "today", "yesterday", specific dates

## 🏗️ Workflow Architecture

### Two Main Components:

#### **Component 1: Expense Logging** (Telegram → Google Sheets)
```
Telegram Trigger (User sends message)
    ↓
Date Parser (Extract date, format for AI)
    ↓
Expense Parser Agent (AI)
    ├── Parse expense details (amount, category, description)
    ├── Google Sheets Tool (Log to spreadsheet)
    └── Conversation Memory (Remember context)
    ↓
Format Confirmation (Create response message)
    ↓
Send Confirmation (Telegram reply)
```

#### **Component 2: Monthly Reporting** (Automated)
```
Schedule Trigger (1st of each month at 9 AM)
    ↓
Fetch Monthly Data (Google Sheets)
    ↓
Calculate Totals & Analyze (JavaScript)
    ↓
Generate Report (AI)
    ├── Spending summary by category
    ├── Insights and trends
    └── Recommendations
    ↓
Send Report (Telegram)
```

## 📋 Prerequisites

### Required Accounts & Credentials

1. **n8n Instance** (Cloud or Self-hosted)
   - [Get started with n8n](https://n8n.io/)
   - Requires LangChain support

2. **Telegram Bot** (Free)
   - Create bot via [@BotFather](https://t.me/botfather)
   - Get bot token
   - No special permissions needed

3. **Google Account** (Free)
   - For Google Sheets access
   - OAuth2 authentication

4. **Google Gemini API** (Free tier available)
   - Sign up at [Google AI Studio](https://makersuite.google.com/app/apikey)
   - Generate API key
   - Free tier: 60 requests/minute

### Technical Requirements

- n8n version 1.0.0 or higher with LangChain support
- Google Sheets API enabled
- Internet connection for API calls

## 🚀 Installation

### Step 1: Create Telegram Bot

1. **Start BotFather**:
   - Open Telegram, search for [@BotFather](https://t.me/botfather)
   - Send `/newbot`

2. **Configure Bot**:
   - Choose a name: e.g., "My Expense Tracker"
   - Choose a username: e.g., "myexpensetracker_bot"
   - Copy the bot token (looks like: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyz`)

3. **Optional Customization**:
   - Send `/setdescription` to add bot description
   - Send `/setabouttext` to add about text
   - Send `/setuserpic` to add profile picture

4. **Start Your Bot**:
   - Search for your bot in Telegram
   - Send `/start` to initialize

### Step 2: Set Up Google Sheets

1. **Create Spreadsheet**:
   - Go to [Google Sheets](https://sheets.google.com)
   - Click "Blank" to create new spreadsheet
   - Name it: "Expense Tracker"

2. **Set Up Expense Sheet**:
   - Rename "Sheet1" to "EXPENSE LOGGER"
   - Add headers in Row 1:
     ```
     A1: DATE
     B1: AMOUNT
     C1: CATEGORY
     D1: DESCRIPTION
     ```

3. **Format Columns**:
   - Column A (DATE): Plain text
   - Column B (AMOUNT): Number format with no decimals
   - Column C (CATEGORY): Plain text
   - Column D (DESCRIPTION): Plain text

4. **Optional: Add Data Validation**:
   - Select column C (CATEGORY)
   - Data → Data validation
   - Criteria: List of items
   - Items: `Food, Transport, Shopping, Entertainment, Bills, Healthcare, Other`

5. **Copy Spreadsheet ID**:
   - Look at URL: `https://docs.google.com/spreadsheets/d/[SPREADSHEET_ID]/edit`
   - Copy the ID between `/d/` and `/edit`

### Step 3: Get Google Gemini API Key

1. Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Sign in with Google account
3. Click "Create API Key"
4. Copy the key (starts with `AIza...`)
5. Store securely

### Step 4: Import n8n Workflow

1. Download `Telegram_Expense_Tracker.json`
2. Open n8n instance
3. Click **Workflows** → **Import from File**
4. Select the JSON file
5. Click **Import**

### Step 5: Configure Credentials

#### A. Telegram Bot API

1. Click **"Telegram Expense Input"** node
2. Click **Credentials** → **Create New**
3. Select **Telegram API**
4. Enter your bot token
5. Test connection
6. Save

Use the same credential for:
- "Telegram Expense Input"
- "Send Confirmation"
- "Send Monthly Report" (if enabled)

#### B. Google Gemini API

1. Click **"Google Gemini Chat Model"** node
2. Click **Credentials** → **Create New**
3. Select **Google PaLM/Gemini API**
4. Enter your API key
5. Save

#### C. Google Sheets OAuth2

1. Click **"Google Sheets"** node (the one that logs expenses)
2. Click **Credentials** → **Create New**
3. Select **Google Sheets OAuth2 API**
4. Follow OAuth flow:
   - Sign in with Google
   - Allow access to Google Sheets
   - Authorize n8n
5. Save

Use the same credential for:
- "Google Sheets" (logging node)
- "Fetch Monthly Expenses" (reporting node, if using)

### Step 6: Configure Google Sheets Document ID

1. Click **"Google Sheets"** node
2. In **Document** field:
   - Select "By URL" or "By ID"
   - Enter your spreadsheet URL or ID
3. In **Sheet** field:
   - Select your sheet name: "EXPENSE LOGGER"
4. Verify column mappings:
   - DATE → DATE
   - AMOUNT → AMOUNT
   - CATEGORY → CATEGORY
   - DESCRIPTION → DESCRIPTION

### Step 7: Customize Settings (Optional)

#### Change Currency

Default is Nigerian Naira (₦). To change:

1. Click **"Expense Parser Agent"** node
2. Find in system message:
   ```
   **Currency:** All amounts are in Nigerian Naira (NGN), denoted with '₦'
   ```
3. Replace with your currency:
   ```
   **Currency:** All amounts are in US Dollars (USD), denoted with '$'
   ```

#### Adjust Categories

1. Click **"Expense Parser Agent"** node
2. Find:
   ```
   **Expense Categories:** Food, Transport, Shopping, Entertainment, Bills, Healthcare, Other
   ```
3. Add/remove categories as needed:
   ```
   **Expense Categories:** Food, Transport, Shopping, Entertainment, Bills, Healthcare, Education, Subscriptions, Other
   ```

#### Enable Monthly Reports

1. Find **"Schedule Trigger"** node (for monthly reports)
2. Configure:
   - **Trigger At**: 1st of every month
   - **Time**: 9:00 AM (or your preference)
3. Ensure it's connected to reporting nodes
4. Activate the trigger

### Step 8: Test the Workflow

#### Test Expense Logging:

1. Open your Telegram bot
2. Send test message: "I spent 5000 on food"
3. Check for confirmation message
4. Verify entry in Google Sheets

**Test Messages**:
```
✅ Single expense:
"I spent 5000 on food"
"Spent ₦3500 on transport today"
"Bought groceries for 12000"

✅ Multiple expenses:
"I spent 5000 on food, 3500 on transport, and 2000 on entertainment"
"₦8000 for dinner, ₦15000 for shopping"

✅ With specific dates:
"Yesterday I spent 4000 on lunch"
"On Monday I paid 25000 for bills"
```

#### Test Monthly Report (if enabled):

1. Click **"Schedule Trigger"** node (monthly report)
2. Click **"Execute Node"**
3. Check Telegram for report message
4. Verify data accuracy

### Step 9: Activate Workflow

1. Click the **Active** toggle in top-right corner
2. Workflow is now live!
3. Start tracking your expenses

## 📝 Usage Guide

### Logging Expenses

**Simple Format**:
```
"I spent [amount] on [category/description]"
```

**Examples**:
```
✅ "I spent 5000 on food"
✅ "Spent ₦3500 on uber"
✅ "Bought groceries for 12000"
✅ "Paid 25000 for electricity bill"
✅ "₦8000 for lunch with friends"
```

**Multiple Expenses**:
```
"I spent [amount1] on [item1], [amount2] on [item2], and [amount3] on [item3]"
```

**Examples**:
```
✅ "I spent 5000 on food, 3500 on transport, and 2000 on movies"
✅ "₦8000 for breakfast, ₦15000 for clothes, ₦3000 for taxi"
```

**With Dates**:
```
✅ "Yesterday I spent 4000 on lunch"
✅ "On Monday I paid 25000 for rent"
✅ "Last week I bought shoes for 35000"
```

### Understanding Responses

**Single Expense Confirmation**:
```
✅ Logged: ₦5,000 for Food - Groceries on Monday, January 5, 2026
```

**Multiple Expenses Confirmation**:
```
✅ Logged 3 expenses totaling ₦15,500:
- ₦5,000 - Food
- ₦3,500 - Shopping
- ₦7,000 - Transport
```

### Categories

**Auto-assigned based on keywords**:
- **Food**: food, lunch, dinner, breakfast, groceries, restaurant, snacks
- **Transport**: uber, taxi, bus, fuel, gas, transport, parking
- **Shopping**: clothes, shoes, electronics, shopping, bought
- **Entertainment**: movies, cinema, games, concert, party
- **Bills**: electricity, water, rent, internet, phone, subscription
- **Healthcare**: doctor, hospital, pharmacy, medicine, clinic
- **Other**: anything that doesn't fit above

## ⚙️ Configuration Options

### Customize AI Prompt

Edit **"Expense Parser Agent"** system message to:

**Change Tone**:
```
You are a friendly expense tracking assistant who makes finance management fun!
Use emojis in confirmations and provide encouragement.
```

**Add Budget Warnings**:
```
If the user logs an expense over ₦50,000, include a friendly reminder:
"💡 Large expense alert! Don't forget to review your budget."
```

**Support Multiple Users**:
```
Extract the user's name from the Telegram message and personalize responses.
Example: "Great job tracking your expenses, [Name]!"
```

### Change Date Format

Currently uses: "Monday, January 5, 2026"

To change:

1. Click **"Date Parser"** node
2. Modify JavaScript code:
   ```javascript
   // Current format
   readable_date: now.toLocaleDateString('en-US', { 
     weekday: 'long', 
     year: 'numeric', 
     month: 'long', 
     day: 'numeric' 
   })
   
   // Change to: "05/01/2026"
   readable_date: now.toLocaleDateString('en-GB')
   
   // Or: "Jan 5, 2026"
   readable_date: now.toLocaleDateString('en-US', { 
     year: 'numeric', 
     month: 'short', 
     day: 'numeric' 
   })
   ```

### Add Spending Limits

Add a new node to check if expenses exceed daily/monthly limits:

```javascript
// In a new Code node after "Expense Parser Agent"
const amount = $json.amount;
const dailyLimit = 20000;
const monthlyLimit = 500000;

// Check daily total
const today = new Date().toDateString();
// Fetch today's expenses from Google Sheets
// Calculate total
// Compare with limit

if (todayTotal + amount > dailyLimit) {
  return {
    json: {
      warning: `⚠️ Daily limit exceeded! Today: ₦${todayTotal + amount} (Limit: ₦${dailyLimit})`
    }
  };
}
```

### Export to Other Platforms

Add nodes to sync with:
- **Notion**: Create database entries
- **Airtable**: Store in organized base
- **Email**: Weekly summary emails
- **WhatsApp**: Duplicate to WhatsApp (if you use both)

## 📊 Monthly Report Features

The automated monthly report includes:

**Summary Statistics**:
- Total expenses for the month
- Average daily spending
- Highest single expense
- Most frequent category

**Category Breakdown**:
- Spending by category with percentages
- Visual representation (text-based chart)
- Comparison to previous month

**Insights**:
- Spending trends
- Budget recommendations
- Savings opportunities
- Unusual spending patterns

**Example Report**:
```
📊 MONTHLY EXPENSE REPORT - January 2026

💰 Total Spent: ₦458,750
📅 Daily Average: ₦14,798
🔝 Largest Expense: ₦85,000 (Bills - Rent)

📈 CATEGORY BREAKDOWN:
• Food: ₦125,000 (27%)
• Transport: ₦65,000 (14%)
• Bills: ₦125,000 (27%)
• Shopping: ₦90,000 (20%)
• Entertainment: ₦35,000 (8%)
• Healthcare: ₦18,750 (4%)

💡 INSIGHTS:
✓ You spent 15% less than last month
✓ Food expenses increased by ₦20,000
✓ Great job keeping entertainment under ₦40,000!

🎯 RECOMMENDATIONS:
• Consider meal planning to reduce food costs
• You're on track with your budget goals
• Review shopping expenses - potential savings area
```

## 🔧 Advanced Customization

### Add Receipt Photo Support

Extend to accept photos of receipts:

1. Add OCR integration (Google Vision API)
2. Extract amount from receipt image
3. Parse and log automatically

### Multi-Currency Support

Handle expenses in different currencies:

1. Add currency detection to AI prompt
2. Store original currency and amount
3. Convert to base currency for reporting
4. Use exchange rate API (e.g., exchangerate-api.com)

### Budget Tracking

Add budget management:

1. Create "Budgets" sheet with category limits
2. Check against budget before logging
3. Send alerts when nearing limits
4. Visual progress bars in monthly report

### Family Sharing

Support multiple family members:

1. Identify user from Telegram username
2. Add "USER" column to Google Sheets
3. Generate per-user and combined reports
4. Compare family member spending

### Investment Tracking

Extend to track income and investments:

1. Add "Income" category
2. Create separate sheet for investments
3. Calculate net savings
4. Track net worth over time

## 📈 Analytics & Insights

### View Your Data

**In Google Sheets**:
- Create pivot tables for analysis
- Build charts for visualization
- Calculate monthly averages
- Identify spending patterns

**Formulas to Add**:
```
// Monthly total
=SUMIF(A:A, ">="&DATE(2026,1,1), B:B)

// Category total
=SUMIF(C:C, "Food", B:B)

// Daily average
=AVERAGE(B:B)

// Highest expense
=MAX(B:B)
```

**Recommended Charts**:
- Pie chart: Spending by category
- Line chart: Daily spending over time
- Bar chart: Monthly comparison
- Stacked area: Cumulative spending

### Export Data

**To CSV**:
- File → Download → CSV

**To PDF**:
- File → Download → PDF

**To Other Tools**:
- Use Google Sheets API
- Import into Excel, Numbers, etc.
- Connect to data visualization tools (Tableau, Looker)

## ⚠️ Important Notes

### Data Privacy

**Your data is stored**:
- Google Sheets (your Google account)
- n8n workflow memory (temporary)
- Telegram messages (Telegram's servers)

**Best practices**:
- Use strong passwords
- Enable 2FA on all accounts
- Regularly backup your spreadsheet
- Review access permissions

### Currency Formatting

**Current**: Nigerian Naira (₦)

**Supported by Google Sheets**:
- Use custom number format: `₦#,##0`
- Or format manually in sheets

**For other currencies**:
- Update AI prompt with your currency symbol
- Adjust number formatting in confirmation messages

### Message Limits

**Telegram**:
- Max message length: 4,096 characters
- Monthly reports may be truncated if very long
- Consider sending as multiple messages or file

**n8n**:
- No specific limits on workflow executions
- Google Sheets API has rate limits (60 requests/minute per user)

### Costs

**All services have free tiers**:
- Telegram Bot: Free, unlimited
- Google Sheets: Free (15 GB across Google Drive)
- Google Gemini: Free tier (60 requests/minute)
- n8n Cloud: Free tier (5,000 executions/month)

**Estimated usage**:
- 10 expenses/day = 300 executions/month
- Well within all free tiers
- Potential paid needs only for high-volume business use

## 🐛 Troubleshooting

### Expenses Not Logging

**Symptom**: Message sent but no confirmation

**Solutions**:
1. Check Telegram bot token is correct
2. Verify Google Sheets credentials are valid
3. Ensure sheet name matches exactly ("EXPENSE LOGGER")
4. Check column headers match (DATE, AMOUNT, CATEGORY, DESCRIPTION)
5. Review execution logs in n8n for errors

### AI Not Understanding Messages

**Symptom**: Wrong category or amount parsed

**Solutions**:
1. Be more explicit: "I spent ₦5000" instead of "spent 5k"
2. Check Gemini API key is valid
3. Review AI prompt for clarity
4. Add more examples to the prompt
5. Use standard category names

### Confirmation Message Not Sent

**Symptom**: Logged in sheets but no Telegram reply

**Solutions**:
1. Check Telegram credentials in "Send Confirmation" node
2. Verify bot has permission to send messages
3. Check Telegram API rate limits
4. Review execution logs

### Wrong Date

**Symptom**: Dates are incorrect or in wrong format

**Solutions**:
1. Check timezone settings in n8n
2. Verify date format in "Date Parser" node
3. Ensure system time is correct
4. Test with explicit dates: "On January 5 I spent..."

### Monthly Report Not Sent

**Symptom**: 1st of month but no report

**Solutions**:
1. Check Schedule Trigger is active
2. Verify trigger time is correct (timezone)
3. Ensure Google Sheets has data for previous month
4. Test by manually executing the trigger

## 🔒 Security Best Practices

- **Never share your bot token** publicly
- **Use OAuth2** for Google Sheets (more secure than service accounts)
- **Enable 2FA** on Google account
- **Rotate API keys** every 6 months
- **Review access logs** in Google account regularly
- **Backup your data** monthly
- **Use private Telegram bot** (don't add to public groups)
- **Monitor for unusual expenses** (someone else accessing?)

## 💡 Pro Tips

1. **Set Reminders**: Create Telegram reminder to log expenses daily
2. **Batch Entry**: Log all expenses at end of day in one message
3. **Use Voice**: Telegram voice-to-text + this bot = hands-free logging
4. **Round Numbers**: ₦5,000 instead of ₦4,987 (easier tracking)
5. **Be Consistent**: Use same descriptions for recurring expenses
6. **Review Weekly**: Check sheet every Sunday for errors
7. **Set Goals**: Add budget goals in separate sheet tab
8. **Compare Months**: Create charts to see progress
9. **Share Selectively**: Add family member's Telegram to same bot
10. **Automate More**: Connect to bank API for automatic expense import

## 📈 Success Metrics

Track your progress:

**Engagement**:
- Expenses logged per week
- Consistency (days tracked vs missed)
- Time saved vs manual entry

**Financial Health**:
- Monthly spending trend (up/down)
- Budget adherence rate
- Savings rate improvement
- Emergency fund growth

**Goals** (3-month targets):
- 95%+ expense tracking rate
- Reduce discretionary spending by 10%
- Increase savings by 15%
- Zero budget overruns

## 🤝 Contributing

Ideas for improvements:
- Add receipt OCR support
- Create mobile app interface
- Build prediction model for future expenses
- Add investment tracking
- Create shared family accounts
- Implement bill reminders
- Add savings goal tracking
- Build comparison with peers (anonymized)

## 📝 License

This workflow is provided as-is for personal and commercial use.

## 📞 Support

For issues with:
- **n8n**: [n8n Community Forum](https://community.n8n.io/)
- **Telegram Bots**: [Telegram Bot API](https://core.telegram.org/bots)
- **Google Sheets**: [Google Sheets Help](https://support.google.com/sheets)
- **Google Gemini**: [Google AI Documentation](https://ai.google.dev/docs)

---

**Built with ❤️ for better financial wellness**

*Last Updated: February 2026*
