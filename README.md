# 📞 Twilio Voice Call Automation

A simple yet powerful Python script for making automated voice calls using Twilio's API. Perfect for notifications, reminders, alerts, and automated customer communication.

![Python](https://img.shields.io/badge/Python-3.6+-blue.svg)
![Twilio](https://img.shields.io/badge/Twilio-API-red.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

## ✨ Features

### 📱 Automated Voice Calls
- **Instant Calling**: Make calls programmatically via Twilio API
- **Text-to-Speech**: Convert text to natural voice using TwiML
- **Simple Setup**: Just 15 lines of code
- **Environment Variables**: Secure credential management
- **Call Tracking**: Get SID for call monitoring

### 🎯 Use Cases
- Emergency notifications
- Appointment reminders
- Customer alerts
- System monitoring alerts
- Marketing campaigns
- Two-factor authentication
- Delivery notifications
- Survey calls

## 📋 Requirements

### System Requirements
- Python 3.6 or higher
- Internet connection
- Twilio account (free trial available)

### Python Dependencies
```
twilio>=8.0.0
python-dotenv>=1.0.0
```

## 🔧 Installation

### 1. Install Dependencies

```bash
pip install twilio python-dotenv
```

Or use requirements.txt:
```bash
pip install -r requirements.txt
```

**requirements.txt:**
```
twilio>=8.0.0
python-dotenv>=1.0.0
```

### 2. Set Up Twilio Account

#### Create Twilio Account
1. Go to [twilio.com/try-twilio](https://www.twilio.com/try-twilio)
2. Sign up for a free account
3. Verify your email and phone number

#### Get Credentials
1. Go to [Twilio Console](https://console.twilio.com)
2. Find your **Account SID** (starts with `AC...`)
3. Find your **Auth Token** (click to reveal)
4. Copy both values

#### Get Phone Number
1. In Twilio Console, go to **Phone Numbers** → **Manage** → **Buy a number**
2. Select a number (free on trial)
3. Note down your Twilio phone number (e.g., `+1234567890`)

#### Verify Destination Numbers (Trial Accounts)
For trial accounts, you must verify numbers before calling:
1. Go to **Phone Numbers** → **Verified Caller IDs**
2. Click **Add a new number**
3. Enter the phone number you want to call
4. Complete verification (SMS or voice call)

### 3. Configure Environment Variables

Create a `.env` file in your project directory:

```env
account_sid=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
auth_token=your_auth_token_here
from_=+1234567890
to=+0987654321
```

**Important**: 
- Use your actual Twilio credentials
- `from_` must be your Twilio phone number
- `to` must be a verified number (for trial accounts)
- Include country code (e.g., +1 for US)

**Security Note**: Never commit your `.env` file to version control!

Add to `.gitignore`:
```
.env
*.pyc
__pycache__/
```

## 🚀 Usage

### Basic Usage

Save the script as `call.py` and run:

```bash
python call.py
```

Expected output:
```
Call SID: CAxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Code Explanation

```python
from twilio.rest import Client
import os
from dotenv import load_dotenv

# Load environment variables from .env file
load_dotenv()

# Get credentials from environment
account_sid = os.getenv("account_sid")
auth_token = os.getenv("auth_token")

# Initialize Twilio client
client = Client(account_sid, auth_token)

# Make the call
call = client.calls.create(
    to=os.getenv("to"),           # Destination number
    from_=os.getenv("from_"),     # Your Twilio number
    twiml='<Response><Say>HOW are you</Say></Response>'  # Message to speak
)

# Print call identifier
print("Call SID:", call.sid)
```

## 🎨 Customization

### Change Voice Message

```python
# Simple message
twiml='<Response><Say>Hello, this is a test call.</Say></Response>'

# Multiple sentences
twiml='<Response><Say>Hello! This is an important reminder. Please call us back.</Say></Response>'

# With pauses
twiml='<Response><Say>Hello.</Say><Pause length="2"/><Say>This is your reminder.</Say></Response>'
```

### Change Voice Settings

```python
# Different voice (man, woman, alice)
twiml='<Response><Say voice="woman">Hello, this is a woman\'s voice.</Say></Response>'

# Different language
twiml='<Response><Say language="es-ES">Hola, ¿cómo estás?</Say></Response>'

# Slower speech
twiml='<Response><Say voice="woman" language="en-US">This is spoken slowly.</Say></Response>'
```

Available voices:
- `man` - Male voice (default)
- `woman` - Female voice
- `alice` - Premium voice (more natural)

Available languages:
- `en-US` - English (US)
- `en-GB` - English (UK)
- `es-ES` - Spanish (Spain)
- `es-MX` - Spanish (Mexico)
- `fr-FR` - French
- `de-DE` - German
- `it-IT` - Italian
- `pt-BR` - Portuguese (Brazil)
- [Full list](https://www.twilio.com/docs/voice/twiml/say#attributes)

### Play Audio File

```python
twiml='<Response><Play>https://example.com/audio.mp3</Play></Response>'
```

### Gather User Input

```python
twiml='''<Response>
    <Gather input="dtmf" numDigits="1" action="https://your-server.com/handle-input">
        <Say>Press 1 for sales, press 2 for support.</Say>
    </Gather>
</Response>'''
```

### Record Call

```python
call = client.calls.create(
    to=os.getenv("to"),
    from_=os.getenv("from_"),
    twiml='<Response><Say>Please leave a message after the beep.</Say><Record maxLength="30"/></Response>'
)
```

### Call Multiple Numbers

```python
numbers = ["+1234567890", "+0987654321", "+1122334455"]

for number in numbers:
    call = client.calls.create(
        to=number,
        from_=os.getenv("from_"),
        twiml='<Response><Say>This is an automated notification.</Say></Response>'
    )
    print(f"Call to {number} - SID: {call.sid}")
```

## 🎯 Advanced Examples

### 1. Emergency Alert System

```python
from twilio.rest import Client
import os
from dotenv import load_dotenv

load_dotenv()

def send_emergency_alert(message, phone_numbers):
    """Send emergency alert to multiple numbers"""
    client = Client(os.getenv("account_sid"), os.getenv("auth_token"))
    
    twiml = f'<Response><Say voice="woman">ALERT! {message}</Say></Response>'
    
    call_sids = []
    for number in phone_numbers:
        try:
            call = client.calls.create(
                to=number,
                from_=os.getenv("from_"),
                twiml=twiml
            )
            call_sids.append(call.sid)
            print(f"✅ Alert sent to {number} - SID: {call.sid}")
        except Exception as e:
            print(f"❌ Failed to call {number}: {e}")
    
    return call_sids

# Usage
emergency_contacts = ["+1234567890", "+0987654321"]
send_emergency_alert("Server is down! Immediate attention required.", emergency_contacts)
```

### 2. Appointment Reminder

```python
def send_appointment_reminder(patient_name, appointment_time, phone):
    """Send appointment reminder"""
    message = f"Hello {patient_name}. This is a reminder about your appointment on {appointment_time}. Please call if you need to reschedule."
    
    twiml = f'<Response><Say voice="woman">{message}</Say></Response>'
    
    client = Client(os.getenv("account_sid"), os.getenv("auth_token"))
    call = client.calls.create(
        to=phone,
        from_=os.getenv("from_"),
        twiml=twiml
    )
    
    return call.sid

# Usage
send_appointment_reminder("John Doe", "tomorrow at 2 PM", "+1234567890")
```

### 3. Scheduled Calls

```python
import schedule
import time

def make_daily_reminder():
    """Daily reminder call"""
    client = Client(os.getenv("account_sid"), os.getenv("auth_token"))
    
    call = client.calls.create(
        to=os.getenv("to"),
        from_=os.getenv("from_"),
        twiml='<Response><Say>This is your daily reminder to take your medication.</Say></Response>'
    )
    print(f"Daily reminder sent - SID: {call.sid}")

# Schedule call for 9 AM every day
schedule.every().day.at("09:00").do(make_daily_reminder)

print("Scheduler started. Press Ctrl+C to stop.")
while True:
    schedule.run_pending()
    time.sleep(60)
```

### 4. Interactive Voice Response (IVR)

```python
from flask import Flask, request
from twilio.twiml.voice_response import VoiceResponse

app = Flask(__name__)

@app.route("/voice", methods=['GET', 'POST'])
def voice():
    """Respond to incoming calls"""
    response = VoiceResponse()
    
    response.say("Welcome to our service. Press 1 for sales, press 2 for support.")
    
    gather = response.gather(num_digits=1, action='/handle-key')
    
    return str(response)

@app.route("/handle-key", methods=['GET', 'POST'])
def handle_key():
    """Handle user input"""
    digit_pressed = request.values.get('Digits', None)
    response = VoiceResponse()
    
    if digit_pressed == "1":
        response.say("Connecting you to sales.")
        response.dial("+1234567890")  # Sales number
    elif digit_pressed == "2":
        response.say("Connecting you to support.")
        response.dial("+0987654321")  # Support number
    else:
        response.say("Invalid input. Please try again.")
        response.redirect('/voice')
    
    return str(response)

# Make call to IVR
call = client.calls.create(
    to=os.getenv("to"),
    from_=os.getenv("from_"),
    url="https://your-server.com/voice"  # Your Flask app URL
)
```

### 5. Call Status Tracking

```python
def track_call_status(call_sid):
    """Track call status"""
    client = Client(os.getenv("account_sid"), os.getenv("auth_token"))
    
    call = client.calls(call_sid).fetch()
    
    return {
        'sid': call.sid,
        'status': call.status,
        'duration': call.duration,
        'direction': call.direction,
        'from': call.from_formatted,
        'to': call.to_formatted,
        'price': call.price,
        'date_created': call.date_created
    }

# Usage
call = client.calls.create(
    to=os.getenv("to"),
    from_=os.getenv("from_"),
    twiml='<Response><Say>Test call</Say></Response>'
)

import time
time.sleep(5)  # Wait for call to process

status = track_call_status(call.sid)
print(f"Call Status: {status['status']}")
print(f"Duration: {status['duration']} seconds")
```

## 🛠️ Troubleshooting

### Authentication Error

**Error: "Unable to create record: Authenticate"**

```bash
# Check credentials in .env
cat .env

# Verify on Twilio Console
# https://console.twilio.com
```

**Solution**: 
- Ensure `account_sid` starts with `AC`
- Ensure `auth_token` is correct
- Check for spaces or quotes in `.env` file

### Invalid Phone Number

**Error: "The 'To' number +... is not a valid phone number"**

**Solution**:
- Include country code (e.g., +1 for US)
- Remove spaces, dashes, parentheses
- Verify number format: `+1234567890`

### Unverified Number (Trial Account)

**Error: "The number +... is unverified"**

**Solution**:
1. Go to [Twilio Console](https://console.twilio.com)
2. Navigate to **Phone Numbers** → **Verified Caller IDs**
3. Add and verify the destination number
4. Complete SMS or voice verification

### Invalid TwiML

**Error: "Unable to create record: Invalid TwiML"**

```python
# ❌ Wrong - missing quotes
twiml='<Response><Say>Hello</Say></Response>'

# ✅ Correct
twiml='<Response><Say>Hello</Say></Response>'

# ✅ For complex TwiML use triple quotes
twiml='''<Response>
    <Say>Hello</Say>
    <Pause length="1"/>
    <Say>Goodbye</Say>
</Response>'''
```

### Module Not Found

**Error: "No module named 'twilio'"**

```bash
# Install twilio
pip install twilio

# Verify installation
python -c "import twilio; print(twilio.__version__)"
```

### Environment Variables Not Loading

**Error: Variables are None**

```python
# Debug environment variables
import os
from dotenv import load_dotenv

load_dotenv()

print("account_sid:", os.getenv("account_sid"))
print("auth_token:", os.getenv("auth_token"))
print("from_:", os.getenv("from_"))
print("to:", os.getenv("to"))

# Ensure .env file exists in same directory
# Ensure no typos in variable names
```

## 💰 Pricing

### Trial Account
- **Free Credit**: $15.50 USD
- **Outbound Calls**: ~$0.02 per minute
- **Free Minutes**: ~775 minutes with trial credit
- **Limitations**: Can only call verified numbers

### Paid Account
- **Pay As You Go**: No monthly fees
- **Outbound Calls**: $0.013-$0.05 per minute (varies by country)
- **Phone Number**: $1.00 per month
- **No Verification**: Call any number

### Cost Examples
```
10 one-minute calls = $0.20
100 one-minute calls = $2.00
1000 one-minute calls = $20.00

Plus phone number: $1/month
```

## 📊 Call Statuses

| Status | Description |
|--------|-------------|
| `queued` | Call is waiting to be made |
| `ringing` | Phone is ringing |
| `in-progress` | Call is active |
| `completed` | Call finished successfully |
| `busy` | Recipient's line was busy |
| `failed` | Call failed |
| `no-answer` | No one answered |
| `canceled` | Call was canceled |

## 🔒 Security Best Practices

### 1. Never Hardcode Credentials
```python
# ❌ NEVER do this
account_sid = "ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
auth_token = "your_token_here"

# ✅ Always use environment variables
account_sid = os.getenv("account_sid")
auth_token = os.getenv("auth_token")
```

### 2. Secure Your .env File
```bash
# Add to .gitignore
echo ".env" >> .gitignore

# Set proper permissions (Linux/Mac)
chmod 600 .env
```

### 3. Rotate Credentials Regularly
- Change auth token every 90 days
- Use Twilio Console to generate new tokens
- Update `.env` file

### 4. Use Environment-Specific Variables
```python
# Development
.env.development

# Production
.env.production

# Load based on environment
env_file = '.env.production' if os.getenv('ENV') == 'production' else '.env.development'
load_dotenv(env_file)
```

### 5. Rate Limiting
```python
import time

def make_call_with_rate_limit(numbers, delay=2):
    """Make calls with delay to avoid rate limits"""
    for number in numbers:
        call = client.calls.create(
            to=number,
            from_=os.getenv("from_"),
            twiml='<Response><Say>Message</Say></Response>'
        )
        print(f"Called {number}")
        time.sleep(delay)  # Wait between calls
```

## 🤝 Contributing

Contributions welcome! Ideas for enhancements:

- [ ] Add call recording functionality
- [ ] Implement call scheduling
- [ ] Create web interface
- [ ] Add SMS fallback
- [ ] Implement retry logic
- [ ] Add call analytics
- [ ] Create call templates
- [ ] Add voice recording
- [ ] Implement conference calling
- [ ] Add call forwarding

## 📝 License

This project is licensed under the MIT License.

## 🙏 Acknowledgments

- **Twilio**: Voice API and communication platform
- **python-dotenv**: Environment variable management

## 📞 Support

- **Twilio Docs**: [twilio.com/docs](https://www.twilio.com/docs)
- **Twilio Support**: [support.twilio.com](https://support.twilio.com)
- **API Reference**: [twilio.com/docs/voice/api](https://www.twilio.com/docs/voice/api)

## 🗺️ Roadmap

### Current (v1.0)
- [x] Basic voice calling
- [x] Text-to-speech
- [x] Environment variables

### Next (v1.1)
- [ ] Call recording
- [ ] Status webhooks
- [ ] Error handling
- [ ] Logging

### Future (v2.0)
- [ ] Web interface
- [ ] Database integration
- [ ] Call scheduling
- [ ] Analytics dashboard

## 💡 Tips & Best Practices

### Voice Message Quality
1. **Be Clear**: Use simple, clear language
2. **Be Brief**: Keep messages under 30 seconds
3. **Identify**: Always identify who's calling
4. **Purpose**: State purpose early
5. **Action**: Provide clear next steps

### Testing
1. **Test First**: Always test on your own number
2. **Check Timing**: Test at appropriate times
3. **Volume**: Test message volume and clarity
4. **Edge Cases**: Test busy, no-answer, etc.

### Production Use
1. **Monitoring**: Track call success rates
2. **Logging**: Log all call attempts
3. **Error Handling**: Handle failures gracefully
4. **Compliance**: Follow TCPA and local regulations
5. **Opt-Out**: Provide opt-out mechanism

### Cost Optimization
1. **Batch Calls**: Group calls efficiently
2. **Short Messages**: Keep calls brief
3. **Verify Numbers**: Avoid calling invalid numbers
4. **Monitor Usage**: Track spending
5. **Use Webhook**: Avoid polling for status

---

**Made with ❤️ for automated communication**

*📞 Connect with anyone, anywhere, anytime*

## 📱 Quick Reference

### Common TwiML Commands

```xml
<!-- Speak text -->
<Say>Hello World</Say>

<!-- Play audio -->
<Play>https://example.com/audio.mp3</Play>

<!-- Pause -->
<Pause length="2"/>

<!-- Gather input -->
<Gather input="dtmf" numDigits="1">
    <Say>Press 1</Say>
</Gather>

<!-- Record message -->
<Record maxLength="30"/>

<!-- Dial number -->
<Dial>+1234567890</Dial>

<!-- Hangup -->
<Hangup/>

<!-- Redirect -->
<Redirect>https://example.com/next-step</Redirect>
```

### Phone Number Formats

| Country | Format | Example |
|---------|--------|---------|
| United States | +1XXXXXXXXXX | +12345678901 |
| United Kingdom | +44XXXXXXXXXX | +441234567890 |
| Canada | +1XXXXXXXXXX | +12345678901 |
| Australia | +61XXXXXXXXX | +61234567890 |
| India | +91XXXXXXXXXX | +911234567890 |

### Environment Variables Template

```env
# Twilio Credentials
account_sid=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
auth_token=your_auth_token_here

# Phone Numbers (include country code)
from_=+1234567890
to=+0987654321

# Optional
twilio_phone_number=+1234567890
emergency_contact=+1122334455
```

---

**Need help?** Check the [Twilio documentation](https://www.twilio.com/docs) or open an issue!
