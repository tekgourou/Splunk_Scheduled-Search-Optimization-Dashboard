# Splunk Scheduled Search Optimization Dashboard

A comprehensive Splunk dashboard for analyzing, optimizing, and managing scheduled searches, reports, and alerts across your Splunk environment.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Dashboard Sections](#dashboard-sections)
- [Use Cases](#use-cases)
- [Optimization Recommendations](#optimization-recommendations)
- [Version History](#version-history)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This dashboard provides deep visibility into your Splunk scheduled searches, helping you identify performance bottlenecks, scheduling conflicts, and optimization opportunities. It's designed for Splunk administrators and power users who need to maintain optimal search performance across their deployment.

![Dashboard Overview](images/dashboard_overview.jpg)

## ✨ Features

### Executive Summary
- **Total Scheduled Searches** - Count of all active scheduled items with color-coded thresholds
- **Real-Time Searches Detection** - Identifies critical real-time searches that should be converted to scheduled
- **JOIN Usage Analysis** - Flags searches using the inefficient JOIN command

### Advanced Analytics
- **Cron Schedule Distribution** - Visual breakdown of the top 20 most common scheduling patterns
- **Search Type Classification** - Categorizes searches as Alerts, Reports, or Scheduled Searches
- **Scheduling Conflicts** - Identifies time slots with excessive concurrent searches
- **App-Level Distribution** - Shows which apps have the most scheduled searches

### Detailed Analysis
- **Comprehensive Search Table** - Full details on every scheduled search with:
  - Search name, type, and schedule
  - Frequency and time range
  - App and owner information
  - Optimization flags (Critical, High, Medium, Low)
  - Direct drilldown to search configuration

### Smart Features
- **Clickable Metrics** - Click on "# Searches" in the conflicts table to see specific searches with that schedule
- **Color-Coded Alerts** - Visual indicators for different severity levels
- **Flexible Filtering** - Filter by app and priority level
- **Row-Level Drilldown** - Click any search to view full configuration details

## 🚀 Installation

### Prerequisites
- Splunk Enterprise 8.0+ or Splunk Cloud
- Administrator or Power User role
- Access to the REST API endpoint

### Installation Steps

1. **Download the Dashboard XML**
   ```bash
   wget https://raw.githubusercontent.com/tekgourou/Splunk_Scheduled-Search-Optimization-Dashboard/refs/heads/main/scheduled_search_optimization_dashboard_fixed.xml
   ```

2. **Import to Splunk**
   - Log in to Splunk Web
   - Navigate to **Settings** > **User Interface** > **Views**
   - Click **New from Source**
   - Paste the XML content
   - Set the destination app (recommend creating a dedicated "Search Optimization" app)
   - Click **Save**

3. **Alternative: Manual Installation**
   - Copy the XML file to: `$SPLUNK_HOME/etc/apps/[your_app]/local/data/ui/views/`
   - Restart Splunk or refresh the app

4. **Verify Installation**
   - Navigate to the app where you installed the dashboard
   - Look for "Scheduled Search Optimization Dashboard v2.2"
   - Click **Submit** to load all panels

## 📊 Dashboard Sections

### 1. Executive Summary Cards

**Total Scheduled Searches**
- Shows count of all enabled scheduled searches
- Color ranges:
  - Green (0-50): Healthy
  - Blue (50-100): Moderate
  - Yellow (100-200): High
  - Orange/Red (200+): Critical

**Real-Time Searches (CRITICAL)**
- Identifies searches running continuously
- Color ranges:
  - Green (0): None
  - Yellow (0-500): Few
  - Orange (500-850): Many
  - Red (850+): Excessive
- **Action**: Convert to scheduled searches

**Searches with JOIN**
- Counts searches using the JOIN command
- Color ranges:
  - Green (0): None
  - Yellow (0-5): Few
  - Orange (5-15): Several
  - Red (15+): Many
- **Action**: Optimize using stats, eventstats, or lookups

### 2. Searches by Cron Schedule

Displays the top 20 most common cron schedules with:
- Human-readable frequency (e.g., "Every 5 min")
- Actual cron syntax (e.g., "*/5 * * * *")
- Count of searches using that schedule
- Visual column chart sorted by frequency

### 3. Searches by Type

Pie chart categorizing searches as:
- **Alerts** - Searches with alert actions or tracking enabled
- **Reports** - Searches with email/RSS/summary actions
- **Scheduled Searches** - Everything else

### 4. Scheduling Conflicts Table

Identifies time slots with multiple concurrent searches:
- **Conflict Level** - Color-coded severity (🔴 Critical, 🟡 Warning, 🟢 Moderate, ✓ OK)
- **Cron Schedule** - The exact schedule causing conflicts
- **Time Slot** - Human-readable time (e.g., "Midnight (00:00)")
- **# Searches** - Count (clickable to see list of searches)
- **Minute Pattern** - How minutes are scheduled
- **Hour Pattern** - How hours are scheduled
- **# Apps** - Number of apps affected

**Conflict Thresholds:**
- 🔴 Critical: 20+ searches
- 🟡 Warning: 10-19 searches
- 🟢 Moderate: 5-9 searches
- ✓ OK: <5 searches

### 5. Scheduled Searches by App

Bar chart showing:
- Top 15 apps by scheduled search count
- Helps identify which apps need optimization
- Useful for capacity planning

### 6. Detailed Search Analysis

Comprehensive table with all scheduled searches showing:
- **Search Name** - Title of the search
- **Type** - Alert, Report, or Scheduled Search
- **Schedule Type** - Real-Time or Scheduled
- **Frequency** - Human-readable schedule
- **Freq (min)** - Numeric frequency in minutes
- **Time Range** - Earliest to latest time for the search
- **App** - Application containing the search
- **Owner** - User who owns the search

**Features:**
- Sorted by optimization priority and frequency
- Row numbers for easy reference
- Click any row to view full search configuration
- Color-coded by issue severity (hidden columns for filtering)

## 💡 Use Cases

### 1. Performance Optimization
**Problem**: Splunk searches are slow or timing out  
**Solution**: 
- Filter by "Critical" or "High" priority
- Identify searches with JOIN, real-time, or too-frequent execution
- Optimize top offenders first

### 2. Capacity Planning
**Problem**: Need to understand search load across time  
**Solution**:
- Check "Scheduling Conflicts" table for peak times
- Review "Searches by App" to identify heavy users
- Redistribute searches to off-peak hours

### 3. License Management
**Problem**: Approaching daily index volume limits  
**Solution**:
- Identify frequently-running searches
- Reduce frequency or optimize time ranges
- Convert real-time to scheduled searches

### 4. Security & Compliance
**Problem**: Need audit trail of scheduled content  
**Solution**:
- Use "Detailed Search Analysis" for complete inventory
- Filter by app or owner
- Export table for compliance reporting

### 5. Migration Planning
**Problem**: Moving searches between apps or environments  
**Solution**:
- Use dashboard to inventory all searches by app
- Identify dependencies (same schedules, same time ranges)
- Plan migration windows around low-conflict times

## 🎨 Customization

### Adjusting Thresholds

You can customize the color-coded thresholds by editing the XML:

```xml
<!-- Example: Real-Time Searches threshold -->
<option name="rangeValues">[0,500,850]</option>
<!-- Change to: [0,100,200] for stricter limits -->
```

### Adding Custom Filters

Add new dropdown filters in the `<fieldset>` section:

```xml
<input type="dropdown" token="my_filter">
  <label>My Custom Filter</label>
  <choice value="*">All</choice>
  <default>*</default>
</input>
```

### Modifying Search Queries

All queries use the REST API endpoint:
```spl
| rest /servicesNS/-/-/saved/searches
```

To add custom fields, modify the `fields` command in any panel's query.

## 📈 Version History

### v2.2 (Current)
- Updated real-time search thresholds (0-500-850)
- Improved report detection logic
- Added clickable drilldown to "# Searches" column in conflicts table
- Maintained backward compatibility with simpler SPL

### Reporting Issues

Please include:
- Splunk version
- Dashboard version
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable

## 📝 Best Practices

1. **Regular Monitoring** - Review dashboard weekly
2. **Prioritize by Impact** - Fix Critical and High priority issues first
3. **Document Changes** - Track optimization efforts and results
4. **Test Before Production** - Validate fixes in dev/test environment
5. **Incremental Improvements** - Don't try to fix everything at once
6. **Measure Results** - Track search performance before and after

## 🔒 Security Considerations

- Dashboard requires REST API access to `/servicesNS/-/-/saved/searches`
- Users can only see searches they have permissions to view
- Drilldown links respect user permissions
- No modification of searches - read-only dashboard
- Consider creating a dedicated role with limited permissions

## No Support

## 🙏 Acknowledgments

- Splunk community for best practices
- Claude.ai

---

**Made with ❤️ for the Splunk Community**

*Last Updated: 2025*
