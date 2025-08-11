# VICIdial Reports Documentation

## Table of Contents
1. [Overview](#overview)
2. [Agent Reports](#agent-reports)
3. [Call Analysis Reports](#call-analysis-reports)
4. [Campaign Reports](#campaign-reports)
5. [Performance & Analytics Reports](#performance--analytics-reports)
6. [Real-time & Monitoring Reports](#real-time--monitoring-reports)
7. [Compliance & Regulatory Reports](#compliance--regulatory-reports)
8. [System & Database Information](#system--database-information)

---

## Overview

This VICIdial reporting system provides comprehensive analytics and monitoring capabilities for call center operations. The reports are built using PHP with MySQL database connections and feature interactive web interfaces with filtering, charts, and export capabilities.

**Key Features:**
- Date range filtering for historical analysis
- Agent and campaign-specific filtering
- Real-time data refresh capabilities
- CSV export functionality
- Interactive charts using Chart.js
- Responsive Bootstrap-based UI
- Debug mode for troubleshooting

---

## Agent Reports

### 1. Agent Call Log Report (`agent_call_log_report.php`)

**Purpose:** Provides detailed call-by-call logging for individual or all agents.

**Key Metrics:**
- Total calls per agent
- Sales made
- Total talk time
- Average call duration
- Call disposition tracking

**Features:**
- Filter by date range, agent, campaign
- Call detail table with disposition, duration, lead ID
- Summary statistics cards
- Pagination (1000 records max per view)
- Export to CSV

**Data Sources:** 
- `vicidial_log` (primary)
- `vicidial_users` (agent details)
- `vicidial_campaigns` (campaign names)
- `vicidial_statuses` (disposition descriptions)

---

### 2. Agent Performance Report (`agent_performance_report.php`)

**Purpose:** Comprehensive performance analysis for agents including KPIs and session tracking.

**Key Metrics:**
- Total calls, sales, contacts made
- Conversion rates and contact rates
- Drop rates and talk time analysis
- Calls per agent ratio
- Session login/logout patterns

**Features:**
- Debug mode for troubleshooting
- Two-tab interface: Performance Overview and Session Details
- Advanced filtering by agent and campaign
- Performance grading system
- Time-based compliance tracking

**Data Sources:**
- `vicidial_log` (call data)
- `vicidial_users` (agent information)
- `vicidial_user_log` (session tracking)

---

### 3. Agent Productivity Tracker (`agent_productivity.php`)

**Purpose:** Daily productivity metrics and efficiency analysis.

**Key Metrics:**
- Daily call volume per agent
- Talk time vs session time efficiency
- Sales performance
- Hourly performance trends
- Conversion rates

**Features:**
- Single-day focus with hourly breakdown
- Efficiency percentage calculation
- Visual progress bars for performance indicators
- Hourly performance chart
- Agent comparison table

**Data Sources:**
- `vicidial_log` (call activity)
- `vicidial_users` (agent details)

---

### 4. Agent Schedule & Adherence Report (`agent_schedule_report.php`)

**Purpose:** Schedule compliance and work pattern analysis.

**Key Metrics:**
- Login/logout patterns
- Break time analysis
- Schedule adherence rates
- Agent availability trends
- Performance correlation with schedule

**Features:**
- Weekly pattern analysis
- Break compliance tracking
- Hourly agent availability charts
- Schedule vs performance correlation
- Pause code analysis

**Data Sources:**
- `vicidial_agent_log` (session events)
- `vicidial_users` (agent information)
- `vicidial_log` (call correlation)

---

## Call Analysis Reports

### 5. Call Detail Report (CDR) (`call_detail_report.php`)

**Purpose:** Detailed call record analysis with comprehensive filtering and pagination.

**Key Metrics:**
- Complete call records with metadata
- Call duration and disposition
- Recording availability
- Campaign and agent association

**Features:**
- Advanced filtering (date, campaign, agent, disposition, phone number)
- Pagination for large datasets
- Debug mode with table structure analysis
- Recording file detection
- Adaptive column handling

**Data Sources:**
- `vicidial_log` (primary call data)
- `vicidial_users`, `vicidial_campaigns`, `vicidial_statuses`
- `recording_log` (call recordings)

---

### 6. Call Volume Report (`call_volume_report.php`)

**Purpose:** Call volume trends and patterns across different time periods.

**Key Metrics:**
- Hourly, daily, weekly call volumes
- Peak performance time identification
- Sales correlation with volume
- Agent activity levels

**Features:**
- Three view types: Daily, Hourly, Weekly
- Peak performance ranking
- Dynamic charts based on selected view
- Volume trend analysis

**Data Sources:**
- `vicidial_log` (call volume data)

---

### 7. Disposition Report (`disposition_report.php`)

**Purpose:** Call outcome analysis and disposition effectiveness.

**Key Metrics:**
- Disposition distribution and effectiveness
- Campaign-wise disposition breakdown
- Agent performance by disposition
- Time-based disposition trends

**Features:**
- Disposition effectiveness scoring
- Campaign breakdown by disposition
- Top performer identification
- Trend analysis over time

**Data Sources:**
- `vicidial_log` (disposition data)
- `vicidial_users` (agent information)

---

### 8. Callback Report (`callback_report.php`)

**Purpose:** Callback management and scheduling analysis with intelligent detection.

**Key Metrics:**
- Active, inactive, and overdue callbacks
- Agent callback performance
- Daily callback distribution
- Priority-based callback management

**Features:**
- Multiple detection methods (dedicated table, status-based, auto-calls)
- Intelligent callback source detection
- Priority level assignment
- Daily distribution charts
- Fallback to simulated data for demonstration

**Data Sources:**
- `vicidial_callbacks` (primary)
- `vicidial_log` (callback statuses)
- `vicidial_auto_calls` (alternative callback storage)

---

## Campaign Reports

### 9. Campaign ROI Report (`campaign_roi.php`)

**Purpose:** Return on investment analysis per campaign.

**Key Metrics:**
- Campaign-wise call volume and sales
- Agent utilization per campaign
- Talk time investment analysis
- Revenue per campaign estimation

**Features:**
- Campaign performance comparison
- Agent performance by campaign
- ROI calculation metrics
- Visual campaign comparison charts

**Data Sources:**
- `vicidial_log` (campaign call data)
- `vicidial_users` (agent details)

---

### 10. Campaign Summary Report (`campaign_summary_report.php`)

**Purpose:** Comprehensive campaign performance overview with detailed analytics.

**Key Metrics:**
- Total calls, contacts, sales per campaign
- Drop rates and contact rates
- Conversion rates and agent efficiency
- Call duration analysis

**Features:**
- Two-tab interface: Campaign Summary and Agent Performance
- Advanced filtering and debug mode
- Interactive charts for call volume and conversion rates
- Comprehensive campaign analytics

**Data Sources:**
- `vicidial_log` (call data)
- `vicidial_campaigns` (campaign details)
- `vicidial_users` (agent information)

---

## Performance & Analytics Reports

### 11. Sales Conversion Report (`sales_conversion_report.php`)

**Purpose:** Sales performance tracking and conversion analysis.

**Key Metrics:**
- Sales conversion rates by agent
- Contact-to-sale ratios
- Daily and hourly sales trends
- Revenue estimation and performance grading

**Features:**
- Performance grading system (A+ to D)
- Daily sales trend charts
- Hourly sales pattern analysis
- Top performer showcase
- Revenue tracking

**Data Sources:**
- `vicidial_log` (sales data)
- `vicidial_users`, `vicidial_campaigns`, `vicidial_statuses`

---

### 12. Heatmaps (`heatmaps.php`)

**Purpose:** Visual performance pattern analysis and peak time identification.

**Key Metrics:**
- Call volume heatmaps by time
- Performance intensity mapping
- Peak activity identification
- Resource utilization patterns

**Features:**
- Visual heatmap displays
- Time-based performance patterns
- Peak identification algorithms
- Resource optimization insights

---

## Real-time & Monitoring Reports

### 13. Real-time Dashboard (`realtime_dashboard.php`)

**Purpose:** Live monitoring of call center operations with auto-refresh capabilities.

**Key Metrics:**
- Live agent status (Ready, In-Call, Paused)
- Queue statistics and wait times
- Active call monitoring
- System performance metrics

**Features:**
- Auto-refresh every 10 seconds
- Live agent status tracking
- Queue management display
- Recent activity monitoring
- Real-time system metrics

**Data Sources:**
- `vicidial_live_agents` (live status)
- `vicidial_auto_calls` (queue data)
- `vicidial_closer_log` (inbound activity)
- `vicidial_log` (recent calls)

---

### 14. Real-time Snapshot Report (`realtime_snapshot_report.php`)

**Purpose:** Point-in-time system status and performance snapshot.

**Key Metrics:**
- Current system state
- Agent availability snapshot
- Queue status summary
- Performance indicators

**Features:**
- Snapshot-based reporting
- Current state analysis
- Performance indicators
- System health monitoring

---

## Compliance & Regulatory Reports

### 15. Compliance Report (`compliance_report.php`)

**Purpose:** Regulatory compliance monitoring and violation tracking.

**Key Metrics:**
- DNC (Do Not Call) compliance
- Call time compliance (business hours)
- Agent compliance performance
- Violation analysis and trends

**Features:**
- Multiple compliance categories
- Violation severity tracking
- Daily compliance trends
- Agent compliance scoring
- Regulatory adherence monitoring

**Data Sources:**
- `vicidial_log` (call compliance data)
- `vicidial_dnc` (DNC list)
- `vicidial_users` (agent compliance)

---

## System & Database Information

### 16. Dashboard Overview (`index.php`)

**Purpose:** Main dashboard providing system overview and quick access to all reports.

**Key Metrics:**
- Active agents count
- Today's call statistics
- Queue length and hold times
- Top performer highlights

**Features:**
- Quick access to all reports
- System status indicators
- Auto-refresh toggle
- Performance summaries
- Navigation hub

**Data Sources:**
- `vicidial_live_agents` (current status)
- `vicidial_log` (daily statistics)
- `vicidial_auto_calls` (queue data)

---

## Technical Specifications

### Database Configuration
- **File:** `config/database.php`
- **Connection:** PDO-based MySQL connection
- **Error Handling:** Exception-based error management
- **Character Set:** UTF-8 encoding

### Common Features Across Reports

1. **Filtering System:**
   - Date range selection (start_date, end_date)
   - Agent-specific filtering
   - Campaign-specific filtering
   - Custom parameter filtering

2. **Export Capabilities:**
   - CSV export functionality
   - JavaScript-based client-side export
   - Table data extraction and formatting

3. **User Interface:**
   - Bootstrap-based responsive design
   - FontAwesome icons for visual elements
   - DataTables integration for sorting/pagination
   - Chart.js for interactive visualizations

4. **Debug Features:**
   - Debug mode toggle for troubleshooting
   - Query execution monitoring
   - Table structure analysis
   - Data source verification

5. **Performance Optimizations:**
   - Query optimization with proper indexing
   - Pagination for large datasets
   - Conditional loading of optional data
   - Efficient JOIN operations

### Report Categories Summary

| Category | Reports | Primary Focus |
|----------|---------|---------------|
| **Agent Performance** | 4 reports | Individual agent metrics and productivity |
| **Call Analysis** | 4 reports | Call detail analysis and patterns |
| **Campaign Management** | 2 reports | Campaign performance and ROI |
| **Performance Analytics** | 2 reports | Sales conversion and pattern analysis |
| **Real-time Monitoring** | 2 reports | Live system monitoring and status |
| **Compliance** | 1 report | Regulatory compliance and violations |
| **System Overview** | 1 report | Dashboard and system summary |

### Data Flow Architecture

1. **Data Collection:** VICIdial system populates core tables
2. **Data Processing:** PHP reports query and aggregate data
3. **Data Presentation:** Web interface displays formatted results
4. **Data Export:** CSV generation for external analysis

This comprehensive reporting system provides complete visibility into call center operations, enabling data-driven decision making and performance optimization.
