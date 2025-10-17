# Date and Time Functions

Comprehensive date and time processing capabilities for temporal data manipulation, scheduling, and time-based calculations.

## Core Date/Time Functions

### Basic Date and Time Retrieval
```rexx
-- Current date and time (default UTC, English)
LET today = DATE                            -- "16 Oct 2025" (classic REXX format)
LET currentTime = TIME                      -- "14:30:15" (HH:MM:SS format)
LET timestamp = NOW                         -- "2025-08-29T14:30:15.123Z" (ISO format)

-- Alternative Excel-compatible functions
LET todayExcel = TODAY                      -- Excel-compatible date
LET nowExcel = EXCEL_NOW                    -- Excel-compatible datetime
```

### Extended DATE/TIME with Timezone & Locale

DATE and TIME functions support optional timezone and locale parameters using JavaScript's Intl API:

```rexx
-- DATE and TIME with timezone and locale
DATE(formatCode, timezone, locale)
TIME(formatCode, timezone, locale)

-- Examples with different timezones
LET utcDate = DATE 'N' 'UTC'                      -- "16 Oct 2025" (UTC)
LET nyDate = DATE 'N' 'America/New_York'          -- "16 Oct 2025" (New York time)
LET tokyoDate = DATE 'N' 'Asia/Tokyo'             -- "16 Oct 2025" (Tokyo time)
LET londonDate = DATE 'N' 'Europe/London'         -- "16 Oct 2025" (London time)

-- Examples with different locales (month names in locale language)
LET enDate = DATE 'N' 'UTC' 'en-US'               -- "16 Oct 2025"
LET frDate = DATE 'N' 'UTC' 'fr-FR'               -- "16 oct. 2025" (French)
LET deDate = DATE 'N' 'UTC' 'de-DE'               -- "16 Okt 2025" (German)
LET esDate = DATE 'N' 'UTC' 'es-ES'               -- "16 octubre 2025" (Spanish)
LET jaDate = DATE 'N' 'UTC' 'ja-JP'               -- "16 10月 2025" (Japanese)

-- Combine timezone and locale
LET parisDate = DATE 'N' 'Europe/Paris' 'fr-FR'   -- "16 oct. 2025" (Paris timezone, French)
LET tokyoJaDate = DATE 'N' 'Asia/Tokyo' 'ja-JP'   -- "16 10月 2025" (Tokyo timezone, Japanese)

-- TIME with timezone
LET utcTime = TIME 'N' 'UTC'                       -- "14:30:15"
LET nyTime = TIME 'N' 'America/New_York'          -- "10:30:15" (offset from UTC)
LET tokyoTime = TIME 'N' 'Asia/Tokyo'             -- "23:30:15" (offset from UTC)

-- TIME with format code 'S' (seconds since midnight) and timezone
LET utcSeconds = TIME 'S' 'UTC'                    -- "52215"
LET nySeconds = TIME 'S' 'America/New_York'       -- Seconds since midnight in NY time
```

#### Classic REXX Format Codes

The first parameter supports classic REXX format codes:

- `''` or `'N'` - **Normal format**: "16 Oct 2025" (DD Mon YYYY, space-padded day)
- `'S'` - **Sortable format**: "20251016" (YYYYMMDD)
- `'D'` - **DD/MM/YY format**: "16/10/25"
- `'U'` - **Unpadded DDMMYY**: "16102 5"
- `'W'` - **Day of week**: 0-6 (0=Sunday, 6=Saturday)
- `'B'` - **Base date**: Days since 0001-01-01

```rexx
LET sortableDate = DATE 'S'                  -- "20251016"
LET europeanDate = DATE 'D'                  -- "16/10/25"
LET dayOfWeek = DATE 'W'                     -- "3" (Wednesday)
```

#### Supported Timezones

Any IANA timezone is supported. Common examples:
- UTC: `'UTC'`
- North America: `'America/New_York'`, `'America/Chicago'`, `'America/Denver'`, `'America/Los_Angeles'`, `'America/Vancouver'`
- Europe: `'Europe/London'`, `'Europe/Paris'`, `'Europe/Berlin'`, `'Europe/Madrid'`, `'Europe/Amsterdam'`
- Asia: `'Asia/Tokyo'`, `'Asia/Hong_Kong'`, `'Asia/Shanghai'`, `'Asia/Singapore'`, `'Asia/Bangkok'`, `'Asia/Dubai'`, `'Asia/Kolkata'`
- Australia: `'Australia/Sydney'`, `'Australia/Melbourne'`, `'Australia/Brisbane'`, `'Australia/Perth'`
- Other: `'local'` (system local timezone)

Invalid timezone will throw error: `Invalid timezone: InvalidName/Zone`

#### Supported Locales

A curated whitelist of 50+ BCP 47 locales is supported:

**English:** en-US, en-GB, en-AU, en-CA, en-NZ, en-IE, en-IN, en-ZA

**French:** fr-FR, fr-CA, fr-BE, fr-CH, fr-LU

**German:** de-DE, de-AT, de-CH, de-LU

**Spanish:** es-ES, es-MX, es-AR, es-CL, es-CO, es-PE, es-VE

**Other European:** it-IT, it-CH, pt-PT, pt-BR, nl-NL, nl-BE, sv-SE, sv-FI, no-NO, nb-NO, nn-NO, da-DK, fi-FI, pl-PL, cs-CZ, hu-HU, ro-RO, el-GR, tr-TR

**Slavic:** ru-RU, ru-BY, ru-KZ, uk-UA

**Asian:** ja-JP, zh-CN, zh-TW, zh-HK, zh-SG, ko-KR, th-TH, vi-VN, id-ID, ms-MY

**Middle Eastern/Indian:** ar-SA, ar-AE, ar-EG, ar-IL, he-IL, hi-IN

Unsupported locale will throw error: `Unsupported locale: invalid-locale-code...`

```rexx
-- Error handling for unsupported locale
BEGIN
  LET frDate = DATE 'N' 'UTC' 'invalid-locale'   -- Throws error
RESCUE
  SAY "Invalid locale specified"
END
```

#### Timezone and Locale Examples

```rexx
-- Multilingual team tracking
LET dateUS = DATE 'N' 'America/New_York' 'en-US'    -- "16 Oct 2025" (New York, English)
LET dateEU = DATE 'N' 'Europe/Paris' 'fr-FR'        -- "16 oct. 2025" (Paris, French)
LET dateJP = DATE 'N' 'Asia/Tokyo' 'ja-JP'          -- "16 10月 2025" (Tokyo, Japanese)

-- Business meeting scheduler with locale awareness
LET meetingTimeUTC = TIME 'N' 'UTC'                  -- "14:30:15"
LET meetingTimeNY = TIME 'N' 'America/New_York'     -- "10:30:15"
LET meetingTimeLondon = TIME 'N' 'Europe/London'    -- "14:30:15"
LET meetingTokyo = TIME 'N' 'Asia/Tokyo'            -- "23:30:15"

-- International timestamp logging
LET logUTC = NOW 'UTC'              -- ISO format in UTC
LET logNY = DATE 'N' 'America/New_York' 'en-US'     -- "16 Oct 2025" in New York
SAY "[" || logUTC || "] Event logged in NY: " || logNY
```

### Date Component Extraction
```rexx
-- Extract components from dates
LET dateString = "2025-08-29"
LET currentYear = YEAR date=dateString      -- 2025
LET currentMonth = MONTH date=dateString    -- 8
LET currentDay = DAY date=dateString        -- 29
LET dayOfWeek = WEEKDAY date=dateString     -- 5 (1=Sunday, 7=Saturday)

-- Work with current date
LET today = TODAY
LET thisYear = YEAR date=today
LET thisMonth = MONTH date=today
LET thisDay = DAY date=today
```

### Date Parsing and Validation
```rexx
-- Parse date strings
LET dateStr = "2024-12-25"  
PARSE VAR dateStr WITH year "-" month "-" day

SAY "Parsed date: Year=" || year || ", Month=" || month || ", Day=" || day

-- Validate dates and times
LET validDate = IS_DATE date="2024-03-15"          -- true
LET validTime = IS_TIME time="14:30:00"            -- true
LET validDateTime = IS_DATE date="2024-03-15T14:30:00"  -- true

-- Invalid examples
LET invalidDate = IS_DATE date="2024-13-45"       -- false
LET invalidTime = IS_TIME time="25:70:99"         -- false
```

## Advanced Date Operations

### Date Arithmetic and Calculations
```rexx
-- Age calculation
LET birthYear = 1990
LET currentYear = YEAR date=TODAY
LET age = currentYear - birthYear

SAY "Age: " || age || " years"

-- Days between dates (manual calculation)
LET startDate = "2025-01-01"
LET endDate = "2025-08-29"

-- Extract components for calculation
PARSE VAR startDate WITH startYear "-" startMonth "-" startDay
PARSE VAR endDate WITH endYear "-" endMonth "-" endDay

-- Simple day-of-year approximation
LET startDayOfYear = (startMonth * 30) + startDay
LET endDayOfYear = (endMonth * 30) + endDay
LET daysDiff = endDayOfYear - startDayOfYear

SAY "Approximate days between dates: " || daysDiff
```

### Time Zone and Formatting
```rexx
-- Working with timestamps
LET currentTimestamp = NOW
SAY "Full timestamp: " || currentTimestamp

-- Extract time components from timestamp
LET timeOnly = TIME
PARSE VAR timeOnly WITH hour ":" minute ":" second

SAY "Current time: " || hour || ":" || minute || ":" || second
SAY "Hour: " || hour
SAY "Minute: " || minute
SAY "Second: " || second
```

## Practical Date/Time Examples

### Event Scheduling
```rexx
-- Meeting scheduler
LET meetingDate = "2025-09-15"
LET meetingTime = "14:30:00"

-- Validate the scheduled date
LET isValidDate = IS_DATE date=meetingDate
LET isValidTime = IS_TIME time=meetingTime

IF isValidDate AND isValidTime THEN
  -- Extract meeting details
  LET meetingYear = YEAR date=meetingDate
  LET meetingMonth = MONTH date=meetingDate
  LET meetingDay = DAY date=meetingDate
  LET weekday = WEEKDAY date=meetingDate
  
  -- Convert weekday number to name
  LET weekdayName = ""
  SELECT
    WHEN weekday = 1 THEN LET weekdayName = "Sunday"
    WHEN weekday = 2 THEN LET weekdayName = "Monday"
    WHEN weekday = 3 THEN LET weekdayName = "Tuesday"
    WHEN weekday = 4 THEN LET weekdayName = "Wednesday"
    WHEN weekday = 5 THEN LET weekdayName = "Thursday"
    WHEN weekday = 6 THEN LET weekdayName = "Friday"
    WHEN weekday = 7 THEN LET weekdayName = "Saturday"
  END
  
  SAY "Meeting scheduled for:"
  SAY "  Date: " || weekdayName || ", " || meetingMonth || "/" || meetingDay || "/" || meetingYear
  SAY "  Time: " || meetingTime
ELSE
  SAY "Invalid meeting date or time specified"
ENDIF
```

### Log Timestamp Generation
```rexx
-- Generate consistent log timestamps
LET logEntry = "System startup initiated"
LET currentDate = DATE
LET currentTime = TIME
LET timestamp = NOW

-- Create formatted log entry
LET formattedLog = "[" || timestamp || "] " || logEntry
SAY formattedLog

-- Alternative format with date and time
LET simpleLog = currentDate || " " || currentTime || " - " || logEntry
SAY simpleLog
```

### Business Day Calculation
```rexx
-- Calculate business days (Monday-Friday)
LET checkDate = "2025-08-29"
LET weekdayNum = WEEKDAY date=checkDate

-- Check if it's a business day
LET isBusinessDay = (weekdayNum >= 2 AND weekdayNum <= 6)  -- Monday=2, Friday=6

IF isBusinessDay THEN
  SAY checkDate || " is a business day"
ELSE
  SAY checkDate || " is a weekend"
ENDIF

-- Business hours check
LET currentTime = TIME
PARSE VAR currentTime WITH hour ":" minute ":" second
LET hourNumber = hour

LET isBusinessHours = (hourNumber >= 9 AND hourNumber < 17)  -- 9 AM to 5 PM

IF isBusinessDay AND isBusinessHours THEN
  SAY "Currently during business hours"
ELSE
  SAY "Outside business hours"
ENDIF
```

### Date Range Validation
```rexx
-- Validate date ranges for reports
LET startDate = "2025-01-01"
LET endDate = "2025-12-31"

-- Validate both dates
LET startValid = IS_DATE date=startDate
LET endValid = IS_DATE date=endDate

IF startValid AND endValid THEN
  -- Extract years for comparison
  LET startYear = YEAR date=startDate
  LET endYear = YEAR date=endDate
  
  -- Simple year-based validation
  LET validRange = (endYear >= startYear)
  
  IF validRange THEN
    SAY "Valid date range: " || startDate || " to " || endDate
    
    -- Calculate span
    LET yearSpan = endYear - startYear
    SAY "Report covers " || (yearSpan + 1) || " year(s)"
  ELSE
    SAY "Invalid date range: end date before start date"
  ENDIF
ELSE
  SAY "One or both dates are invalid"
ENDIF
```

### Deadline Tracking
```rexx
-- Project deadline management
LET projectDeadline = "2025-12-15"
LET today = TODAY

-- Extract components for comparison
LET deadlineYear = YEAR date=projectDeadline
LET deadlineMonth = MONTH date=projectDeadline
LET deadlineDay = DAY date=projectDeadline

LET currentYear = YEAR date=today
LET currentMonth = MONTH date=today
LET currentDay = DAY date=today

-- Simple comparison (year, then month, then day)
LET isOverdue = false
IF currentYear > deadlineYear THEN
  LET isOverdue = true
ELSE IF currentYear = deadlineYear THEN
  IF currentMonth > deadlineMonth THEN
    LET isOverdue = true
  ELSE IF currentMonth = deadlineMonth AND currentDay > deadlineDay THEN
    LET isOverdue = true
  ENDIF
ENDIF

-- Calculate approximate days remaining
LET daysInMonth = 30  -- Simplified calculation
LET currentDayOfYear = (currentMonth * daysInMonth) + currentDay
LET deadlineDayOfYear = (deadlineMonth * daysInMonth) + deadlineDay
LET daysRemaining = deadlineDayOfYear - currentDayOfYear

IF isOverdue THEN
  SAY "Project is overdue by approximately " || ABS(value=daysRemaining) || " days"
ELSE IF daysRemaining <= 7 THEN
  SAY "Project deadline approaching in " || daysRemaining || " days!"
ELSE
  SAY "Project deadline: " || daysRemaining || " days remaining"
ENDIF
```

### Time-based File Naming
```rexx
-- Generate timestamped filenames
LET baseFileName = "backup"
LET currentDate = DATE
LET currentTime = TIME

-- Remove colons from time for filename compatibility
LET safeTime = REGEX_REPLACE string=currentTime pattern=":" replacement="-"

-- Create timestamped filename
LET timestampedFile = baseFileName || "_" || currentDate || "_" || safeTime || ".txt"
SAY "Generated filename: " || timestampedFile

-- Alternative format with timestamp
LET isoTimestamp = NOW
-- Replace invalid filename characters
LET safeTimestamp = REGEX_REPLACE string=isoTimestamp pattern="[:T\.]" replacement="-" 
LET isoFileName = baseFileName || "_" || safeTimestamp || ".txt"
SAY "ISO filename: " || isoFileName
```

## Working with Different Date Formats

### Date Format Conversion
```rexx
-- Convert between date formats
LET isoDate = "2025-08-29"           -- ISO format (YYYY-MM-DD)
PARSE VAR isoDate WITH year "-" month "-" day

-- Create US format (MM/DD/YYYY)
LET usFormat = month || "/" || day || "/" || year
SAY "US format: " || usFormat

-- Create European format (DD/MM/YYYY)
LET europeanFormat = day || "/" || month || "/" || year
SAY "European format: " || europeanFormat

-- Create abbreviated format (DD-MMM-YY)
LET monthAbbrev = ""
SELECT
  WHEN month = "01" THEN LET monthAbbrev = "Jan"
  WHEN month = "02" THEN LET monthAbbrev = "Feb"
  WHEN month = "03" THEN LET monthAbbrev = "Mar"
  WHEN month = "04" THEN LET monthAbbrev = "Apr"
  WHEN month = "05" THEN LET monthAbbrev = "May"
  WHEN month = "06" THEN LET monthAbbrev = "Jun"
  WHEN month = "07" THEN LET monthAbbrev = "Jul"
  WHEN month = "08" THEN LET monthAbbrev = "Aug"
  WHEN month = "09" THEN LET monthAbbrev = "Sep"
  WHEN month = "10" THEN LET monthAbbrev = "Oct"
  WHEN month = "11" THEN LET monthAbbrev = "Nov"
  WHEN month = "12" THEN LET monthAbbrev = "Dec"
END

LET shortYear = SUBSTRING string=year start=3 length=2
LET abbreviatedFormat = day || "-" || monthAbbrev || "-" || shortYear
SAY "Abbreviated format: " || abbreviatedFormat
```

### Parsing Various Input Formats
```rexx
-- Handle different input date formats
LET dateInput1 = "2025/08/29"        -- Slash separated
LET dateInput2 = "29-Aug-2025"       -- Day-month-year with abbreviation
LET dateInput3 = "August 29, 2025"   -- Long format

-- Parse slash-separated date
PARSE VAR dateInput1 WITH year1 "/" month1 "/" day1
SAY "Parsed " || dateInput1 || ": " || year1 || "-" || month1 || "-" || day1

-- Parse dash-separated date with month abbreviation
PARSE VAR dateInput2 WITH day2 "-" monthAbbr "-" year2
SAY "Parsed " || dateInput2 || ": Day=" || day2 || ", Month=" || monthAbbr || ", Year=" || year2

-- For complex formats, you might need string processing
LET hasComma = INCLUDES string=dateInput3 substring=","
IF hasComma THEN
  SAY "Long format detected: " || dateInput3
  -- Would require more complex parsing logic
ENDIF
```

## Excel/Spreadsheet Compatibility

### Excel Date Functions
```rexx
-- Excel-compatible date operations
LET today = TODAY                    -- Same as Excel TODAY()
LET now = EXCEL_NOW                  -- Same as Excel NOW()

-- Extract components (Excel compatible)
LET currentYear = YEAR date=today    -- Same as Excel YEAR()
LET currentMonth = MONTH date=today  -- Same as Excel MONTH()
LET currentDay = DAY date=today      -- Same as Excel DAY()
LET weekday = WEEKDAY date=today     -- Same as Excel WEEKDAY()

SAY "Excel compatibility:"
SAY "  TODAY(): " || today
SAY "  NOW(): " || now
SAY "  YEAR(): " || currentYear
SAY "  MONTH(): " || currentMonth
SAY "  DAY(): " || currentDay
SAY "  WEEKDAY(): " || weekday
```

## Error Handling and Edge Cases

### Safe Date Processing
```rexx
-- Handle invalid dates gracefully
LET userInput = "2025-13-45"  -- Invalid date

LET isValid = IS_DATE date=userInput
IF isValid THEN
  LET year = YEAR date=userInput
  LET month = MONTH date=userInput
  LET day = DAY date=userInput
  
  SAY "Valid date: " || month || "/" || day || "/" || year
ELSE
  SAY "Invalid date provided: " || userInput
  -- Use current date as fallback
  LET fallbackDate = TODAY
  SAY "Using current date instead: " || fallbackDate
ENDIF

-- Handle edge cases
LET edgeCase1 = ""              -- Empty string
LET edgeCase2 = "not-a-date"    -- Invalid format

LET valid1 = IS_DATE date=edgeCase1
LET valid2 = IS_DATE date=edgeCase2

SAY "Empty string is valid date: " || valid1      -- false
SAY "Invalid format is valid date: " || valid2    -- false
```

## Function Reference

### Core Date/Time Functions

#### DATE
- **Syntax:** `DATE()`, `DATE(formatCode)`, `DATE(formatCode, timezone)`, `DATE(formatCode, timezone, locale)`
- **Default:** Returns current date as "16 Oct 2025" (classic REXX format, UTC, English)
- **Format Codes:** '' or 'N' (normal), 'S' (sortable YYYYMMDD), 'D' (DD/MM/YY), 'U' (unpadded DDMMYY), 'W' (day of week), 'B' (base date)
- **Timezone:** Any IANA timezone (e.g., 'UTC', 'America/New_York', 'Asia/Tokyo') - throws error if invalid
- **Locale:** BCP 47 locale from 50+ supported locales - throws error if unsupported

#### TIME
- **Syntax:** `TIME()`, `TIME(formatCode)`, `TIME(formatCode, timezone)`, `TIME(formatCode, timezone, locale)`
- **Default:** Returns current time as "14:30:15" (HH:MM:SS, UTC)
- **Format Codes:** '' or 'N' (normal HH:MM:SS), 'S' (seconds since midnight), 'L' (microseconds), 'C' (CPU time), 'E' (elapsed since last call)
- **Timezone:** Any IANA timezone - throws error if invalid
- **Locale:** BCP 47 locale from 50+ supported locales - throws error if unsupported

#### NOW
- **Syntax:** `NOW()` or `NOW(timezone)`, `NOW(timezone, format)`
- **Default:** Returns ISO 8601 timestamp in UTC
- **Timezone:** Any IANA timezone
- **Format:** 'ISO' (default) or custom format string

#### TODAY
- **Syntax:** `TODAY()`
- **Default:** Excel-compatible current date

#### EXCEL_NOW
- **Syntax:** `EXCEL_NOW()`
- **Default:** Excel-compatible current datetime

### Date Component Functions
- `YEAR(date)` - Extract year from date
- `MONTH(date)` - Extract month number from date
- `DAY(date)` - Extract day from date
- `WEEKDAY(date)` - Get day of week (1=Sunday, 7=Saturday)

### Validation Functions
- `IS_DATE(date)` - Validate date format
- `IS_TIME(time)` - Validate time format

### Utility Constants
- **Weekday Numbers**: 1=Sunday, 2=Monday, 3=Tuesday, 4=Wednesday, 5=Thursday, 6=Friday, 7=Saturday
- **Date Formats**: ISO (YYYY-MM-DD), Time (HH:MM:SS), Timestamp (ISO 8601)

**See also:**
- [String Functions](04-string-functions.md) for date string manipulation  
- [Validation Functions](11-validation-functions.md) for date/time validation
- [Excel Functions](14-excel-functions.md) for spreadsheet compatibility
- [Math Functions](05-math-functions.md) for date calculations