# Appointment Reminder

Appointment reminder automation built with **n8n, Google Sheets and Microsoft Outlook**. Designed for appointment-based businesses such as salons and barbershops.

## How it works

1. Runs every day at **09:00 Europe/Dublin**, or manually for testing.
2. Reads appointments from Google Sheets.
3. Keeps rows where the appointment is tomorrow **AND** `reminder_sent` is `NO`.
4. Sends a personalized email through Outlook.
5. Matches the row by `appointment_id` and changes only `reminder_sent` to `YES`.

## Files

- `appointment-reminder.json`: sanitized workflow template, inactive on import.
- `appointments-example.csv`: fictional sample data for Google Sheets.

## Setup

1. Import the JSON into n8n.
2. Create a Google spreadsheet and import the CSV into a tab named `Appointments`.
3. Set `appointment_date` to tomorrow in `yyyy-MM-dd` format. Keep date and time columns as plain text for consistent comparisons. Replace the example email with your own test address.
4. Select your Google Sheets credentials and your document/tab in **both** Google Sheets nodes.
5. Select your Microsoft Outlook credentials in `Send Email Reminder`.
6. Give every appointment a unique `appointment_id`. Use exactly `NO` for pending reminders and `YES` for sent reminders.
7. Run `Manual Test`, confirm the email arrives and the sheet changes to `YES`.
8. Run again: the Filter should discard the row and Outlook should not run. Remove any pinned test data before checking this.
9. Confirm the workflow timezone is `Europe/Dublin`, then publish/activate it. Your n8n instance must be running at the scheduled time.

## Validation performed on the original configured workflow

- Google Sheets successfully returned the test appointment.
- Outlook accepted the message and the test email arrived.
- The update node returned `reminder_sent: YES`.
- With both filter conditions present, the `YES` row was discarded.

The sanitized template was checked as JSON; it must be configured and tested in your own n8n instance. Scheduled execution has not yet been observed.

## Scope and limitations

This version sends email reminders; WhatsApp is a future enhancement. No AI model is required.

## Screenshots

### Workflow overview
Complete automation with manual testing and daily scheduling.

![Workflow overview](images/01-workflow-overview.png)

### Appointment tracking
Google Sheets records the reminder status after sending.

![Appointment tracking](images/02-appointment-tracking.png)

### Email reminder
Personalized email with the appointment details.

![Email reminder](images/03-email-reminder.png)

### Duplicate prevention
The filter discards appointments already marked as sent.

![Duplicate prevention](images/04-duplicate-prevention.png)
The `YES` flag avoids repeat sends during normal sequential runs after a successful update. It is not an exactly-once guarantee: overlapping runs or a sheet update failure after an email is sent can cause duplicates. Outlook accepting a message does not guarantee final delivery. Rescheduled appointments need their flag reset to `NO`. Cancellation handling is not included.

The public template contains no original credential references, spreadsheet IDs, instance metadata, or pinned customer data. Configure your own connections after import.
