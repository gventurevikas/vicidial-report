# Call Detail Report (CDR) Documentation

## Overview
**File:** `call_detail_report.php`  
**Purpose:** Comprehensive call record analysis with detailed filtering, pagination, and recording integration for complete call lifecycle tracking.  
**Category:** Call Analysis Reports  
**Access Level:** Supervisor/Manager/Admin  

---

## Current Fields & Data Structure

### Input Parameters (Advanced Filtering)
| Field | Type | Source | Description | Validation | Advanced Options |
|-------|------|--------|-------------|------------|------------------|
| `start_date` | DATE | GET | CDR analysis start date | Default: 7 days ago | Date range validation |
| `end_date` | DATE | GET | CDR analysis end date | Default: Today | Maximum range limits |
| `campaign` | VARCHAR | GET | Campaign-specific analysis | From `vicidial_campaigns` | Multi-select capability |
| `agent` | VARCHAR | GET | Agent-specific filtering | From `vicidial_users` | Department grouping |
| `disposition` | VARCHAR | GET | Outcome-based filtering | From `vicidial_statuses` | Category grouping |
| `phone_number` | VARCHAR | GET | Customer phone search | Pattern matching | Fuzzy search support |
| `page` | INT | GET | Pagination control | Default: 1 | Performance optimization |
| `debug` | BOOLEAN | GET | System diagnostics | Development mode | Advanced troubleshooting |

### Core CDR Fields
| Field | Database Source | Type | Description | Current Use | KPI Potential | Data Quality |
|-------|----------------|------|-------------|-------------|---------------|--------------|
| **Call Date** | `vl.call_date` | DATETIME | Complete call timestamp | Temporal analysis | ✓ Peak time optimization | Primary sort key |
| **Agent ID** | `vl.user` | VARCHAR(20) | Agent identifier | Performance tracking | ✓ Agent efficiency metrics | Required field |
| **Agent Name** | `vu.full_name` | VARCHAR(100) | Human-readable agent name | Display purposes | ✓ Team performance analysis | Optional enhancement |
| **Campaign ID** | `vl.campaign_id` | VARCHAR(8) | Campaign identifier | Campaign tracking | ✓ Campaign ROI analysis | Critical for attribution |
| **Campaign Name** | `vc.campaign_name` | VARCHAR(40) | Campaign display name | User interface | ✓ Campaign effectiveness | User-friendly display |
| **Phone Number** | `vl.phone_number` | VARCHAR(18) | Customer contact number | Customer identification | ✓ Contact frequency analysis | Data privacy sensitive |
| **Disposition** | `vl.status` | VARCHAR(6) | Call outcome code | Success tracking | ✓ Conversion analysis | Core KPI driver |
| **Disposition Name** | `vs.status_name` | VARCHAR(100) | Human-readable outcome | User understanding | ✓ Outcome categorization | Business clarity |
| **Duration** | `vl.length_in_sec` | INT | Call length in seconds | Efficiency measurement | ✓ Productivity metrics | Quality indicator |
| **Lead ID** | `vl.lead_id` | INT | Lead record identifier | Lead tracking | ✓ Lead conversion funnel | Customer journey |
| **Unique ID** | `vl.uniqueid` | VARCHAR(20) | System call identifier | Record linkage | ✓ Call quality correlation | System integrity |
| **Recording Filename** | `rl.filename` | VARCHAR(255) | Call recording file | Quality assurance | ✓ Training analytics | Optional feature |
| **Recording Location** | `rl.location` | VARCHAR(500) | Recording file path | File management | ✓ Storage optimization | System administration |
| **Term Reason** | `vl.term_reason` | VARCHAR(30) | Call termination cause | Technical analysis | ✓ System reliability | Troubleshooting data |
| **List ID** | `vl.list_id` | INT | Lead list identifier | List performance | ✓ List quality analysis | Marketing attribution |

### Summary Statistics
| Metric | Calculation | Display Format | KPI Application | Business Value |
|--------|-------------|----------------|-----------------|----------------|
| **Total Calls** | `COUNT(vl.uniqueid)` | Formatted number | Volume tracking | Productivity baseline |
| **Sales Made** | `COUNT(CASE WHEN vs.sale='Y')` | Badge with count | Revenue tracking | Success measurement |
| **Total Drops** | `COUNT(CASE WHEN status='DROP')` | Formatted number | Quality indicator | System reliability |
| **Answered Calls** | `COUNT(CASE WHEN duration>0)` | Formatted number | Contact success | Efficiency measurement |
| **Average Duration** | `AVG(length_in_sec)` | HH:MM:SS format | Engagement quality | Customer interaction |
| **Total Duration** | `SUM(length_in_sec)` | HH:MM:SS format | Resource utilization | Capacity planning |
| **Unique Agents** | `COUNT(DISTINCT user)` | Formatted number | Resource deployment | Staffing optimization |
| **Unique Campaigns** | `COUNT(DISTINCT campaign_id)` | Formatted number | Activity diversity | Portfolio management |

---

## Database Schema & Integration

### Primary Data Tables
```sql
-- Core call detail records
vicidial_log (vl) - PRIMARY TABLE
├── uniqueid (VARCHAR(20)) - System call identifier [UNIQUE KEY]
├── call_date (DATETIME) - Call timestamp [INDEXED]
├── user (VARCHAR(20)) - Agent reference [INDEXED]
├── campaign_id (VARCHAR(8)) - Campaign reference [INDEXED]
├── phone_number (VARCHAR(18)) - Customer phone [INDEXED]
├── status (VARCHAR(6)) - Call disposition [INDEXED]
├── length_in_sec (INT) - Call duration
├── lead_id (INT) - Lead reference [FOREIGN KEY]
├── list_id (INT) - List reference [FOREIGN KEY]
├── term_reason (VARCHAR(30)) - Termination cause
├── phone_code (INT) - Geographic/routing code
├── hangup_cause (VARCHAR(10)) - Technical hangup reason
├── dialstatus (VARCHAR(20)) - Dial attempt status
└── call_notes (TEXT) - Agent notes [FUTURE ENHANCEMENT]

-- Call recording integration
recording_log (rl) - OPTIONAL INTEGRATION
├── recording_id (INT) - Recording identifier [PRIMARY KEY]
├── uniqueid (VARCHAR(20)) - Call reference [FOREIGN KEY]
├── filename (VARCHAR(255)) - Recording file name
├── location (VARCHAR(500)) - File storage path
├── start_time (DATETIME) - Recording start
├── end_time (DATETIME) - Recording end
├── filesize (BIGINT) - File size in bytes
└── recording_quality (ENUM) - Audio quality rating [FUTURE]
```

### Supporting Reference Tables
```sql
-- Agent master data
vicidial_users (vu)
├── user (VARCHAR(20)) - Agent login [PRIMARY KEY]
├── full_name (VARCHAR(100)) - Display name
├── user_level (INT) - Permission tier
├── user_group (VARCHAR(20)) - Team assignment
├── active (ENUM('Y','N')) - Account status
├── phone_login (VARCHAR(20)) - Phone extension
├── phone_pass (VARCHAR(10)) - Phone password
└── agent_choose_ingroups (ENUM('0','1')) - Skill flexibility

-- Campaign configuration
vicidial_campaigns (vc)
├── campaign_id (VARCHAR(8)) - Campaign code [PRIMARY KEY]
├── campaign_name (VARCHAR(40)) - Display name
├── campaign_description (TEXT) - Detailed description
├── active (ENUM('Y','N')) - Campaign status
├── dial_method (VARCHAR(10)) - Dialing strategy
├── auto_dial_level (DECIMAL(3,1)) - Automation level
├── campaign_rec_filename (VARCHAR(100)) - Recording settings
└── campaign_script (TEXT) - Agent script

-- Disposition definitions
vicidial_statuses (vs)
├── status (VARCHAR(6)) - Status code [PRIMARY KEY]
├── status_name (VARCHAR(100)) - Human description
├── sale (ENUM('Y','N')) - Revenue indicator
├── dnc (ENUM('Y','N')) - Do Not Call flag
├── customer_contact (ENUM('Y','N')) - Contact indicator
├── not_interested (ENUM('Y','N')) - Interest flag
├── unworkable (ENUM('Y','N')) - Workability flag
└── scheduled_callback (ENUM('Y','N')) - Callback indicator

-- Lead management integration
vicidial_list (vl_list) - EXTENDED INTEGRATION
├── lead_id (INT) - Lead identifier [PRIMARY KEY]
├── phone_number (VARCHAR(18)) - Primary contact
├── first_name (VARCHAR(30)) - Customer first name
├── last_name (VARCHAR(30)) - Customer last name
├── email (VARCHAR(70)) - Email contact
├── source_id (VARCHAR(50)) - Lead source tracking
├── list_id (INT) - List assignment
├── gmt_offset_now (DECIMAL(4,2)) - Timezone data
├── called_count (INT) - Contact attempt count
├── last_local_call_time (DATETIME) - Last contact
└── modify_date (DATETIME) - Record modification
```

---

## Advanced KPI Framework

### Core Performance KPIs
| KPI Category | Metric | Calculation | Target Benchmark | Business Impact |
|--------------|--------|-------------|------------------|-----------------|
| **Volume Efficiency** | Calls per Hour | `COUNT(calls) / hours_active` | 15-25 calls/hour | Productivity measurement |
| **Contact Success** | Answer Rate | `(answered_calls / total_calls) * 100` | >75% | Lead quality indicator |
| **Revenue Generation** | Conversion Rate | `(sales / total_calls) * 100` | 3-8% | Revenue efficiency |
| **Quality Assurance** | Drop Rate | `(drops / total_calls) * 100` | <5% | System/process quality |
| **Customer Engagement** | Average Talk Time | `AVG(duration WHERE duration>0)` | 2-6 minutes | Engagement quality |
| **Resource Utilization** | Agent Utilization | `talk_time / logged_time * 100` | 60-80% | Capacity optimization |

### Advanced Analytics KPIs
| KPI Category | Metric | Calculation Method | Strategic Value |
|--------------|--------|-------------------|-----------------|
| **Predictive Performance** | Success Probability | Machine learning model | Lead prioritization |
| **Customer Lifetime Value** | CLV per Call | Historical revenue analysis | Resource allocation |
| **Quality Score** | Composite Quality Index | Weighted scoring algorithm | Performance benchmarking |
| **Efficiency Ratio** | Output per Input | Multi-factor productivity analysis | Process optimization |
| **Satisfaction Correlation** | NPS vs Call Metrics | Statistical correlation | Customer experience |

---

## Enhanced Data Fields (Future Implementation)

### Immediate Enhancement Opportunities
| Field Name | Data Source | Type | Implementation | KPI Value | Priority |
|------------|-------------|------|----------------|-----------|----------|
| **Customer Sentiment** | Voice analytics | DECIMAL(3,1) | Real-time analysis | ✓ Quality measurement | HIGH |
| **Call Quality Score** | Audio analysis | INT(1-100) | Post-call processing | ✓ Training optimization | HIGH |
| **Lead Score** | ML model | INT(1-1000) | Pre-call calculation | ✓ Prioritization | HIGH |
| **Geographic Data** | Phone number analysis | JSON | Location services | ✓ Regional performance | MEDIUM |
| **Previous Interactions** | Historical analysis | INT | Database aggregation | ✓ Persistence tracking | MEDIUM |
| **Optimal Call Time** | Behavioral analytics | TIME | ML prediction | ✓ Timing optimization | MEDIUM |
| **Agent Skill Match** | Skills matrix | DECIMAL(3,1) | Assignment algorithm | ✓ Efficiency improvement | LOW |
| **Competitor Analysis** | Market data | JSON | External integration | ✓ Strategic positioning | LOW |

### Advanced Integration Fields
| Field Name | External System | Data Type | Sync Method | Business Value |
|------------|-----------------|-----------|-------------|----------------|
| **CRM Customer Data** | Salesforce/HubSpot | JSON | Real-time API | 360° customer view |
| **Payment Information** | Payment processor | ENCRYPTED | Secure integration | Revenue tracking |
| **Support Ticket Status** | Help desk system | VARCHAR(50) | Webhook updates | Service correlation |
| **Marketing Attribution** | Analytics platform | JSON | Batch import | ROI calculation |
| **Social Media Profile** | Social platforms | JSON | API integration | Customer insights |

### Future Innovation Fields
| Field Name | Technology | Data Type | Implementation Timeline | Innovation Value |
|------------|------------|-----------|------------------------|------------------|
| **AI Conversation Analysis** | NLP/GPT | JSON | 6-12 months | Deep insight extraction |
| **Emotional Intelligence Score** | Voice biometrics | DECIMAL(3,1) | 12-18 months | Soft skills measurement |
| **Predictive Call Outcome** | Advanced ML | PROBABILITY | 3-6 months | Resource optimization |
| **Real-time Coaching Suggestions** | AI assistant | TEXT | 9-12 months | Performance improvement |
| **Automated Quality Assessment** | Speech recognition | JSON | 6-9 months | Scalable QA |

---

## Advanced Filtering & Search Capabilities

### Current Filter Implementation
```php
// Basic filtering structure
$where_conditions = [
    "DATE(vl.call_date) BETWEEN ? AND ?",
    // Additional conditions based on user input
];

// Dynamic parameter binding
if (!empty($selected_campaign)) {
    $where_conditions[] = "vl.campaign_id = ?";
    $params[] = $selected_campaign;
}
```

### Enhanced Filter Recommendations
| Filter Type | Implementation | SQL Example | User Benefit |
|-------------|----------------|-------------|--------------|
| **Multi-Select Campaigns** | Array handling | `campaign_id IN (?,?,?)` | Bulk analysis |
| **Date Range Presets** | JavaScript shortcuts | Quick buttons | User convenience |
| **Disposition Grouping** | Category mapping | `status IN (success_group)` | Logical grouping |
| **Duration Ranges** | Slider interface | `length_in_sec BETWEEN ? AND ?` | Efficiency analysis |
| **Agent Teams** | Hierarchical selection | `user IN (team_members)` | Team management |
| **Recording Availability** | Boolean filter | `recording_filename IS NOT NULL` | Quality focus |
| **Lead Source** | Marketing attribution | `source_id = ?` | ROI tracking |
| **Customer Type** | Classification | `customer_segment = ?` | Targeted analysis |

### Advanced Search Features
```javascript
// Proposed search enhancement
const advancedSearch = {
    phoneNumber: {
        type: 'fuzzy',
        operators: ['exact', 'contains', 'starts_with', 'ends_with']
    },
    timeRange: {
        type: 'datetime_range',
        presets: ['today', 'yesterday', 'this_week', 'last_week', 'this_month']
    },
    performance: {
        type: 'calculated_field',
        options: ['above_average', 'below_average', 'top_10_percent']
    },
    customFields: {
        type: 'dynamic',
        source: 'user_defined_fields'
    }
}
```

---

## Performance Optimization Strategy

### Current Optimizations
1. **Database Indexing**
   ```sql
   -- Critical indexes for performance
   CREATE INDEX idx_call_date_agent ON vicidial_log(call_date, user);
   CREATE INDEX idx_campaign_date ON vicidial_log(campaign_id, call_date);
   CREATE INDEX idx_phone_lookup ON vicidial_log(phone_number);
   ```

2. **Query Optimization**
   - Pagination limits (50 records per page)
   - Conditional JOIN operations
   - WHERE clause optimization

3. **Caching Strategy**
   - Agent/Campaign dropdown caching
   - Summary statistics caching
   - Recently accessed data caching

### Advanced Optimization Recommendations
| Optimization Type | Implementation | Performance Gain | Resource Impact |
|------------------|----------------|------------------|-----------------|
| **Partitioning** | Date-based table partitioning | 40-60% query speed | Medium storage overhead |
| **Materialized Views** | Pre-calculated summaries | 70-80% report speed | High storage, CPU overhead |
| **Redis Caching** | In-memory data caching | 50-90% response time | Medium memory usage |
| **Elasticsearch** | Advanced search indexing | 80-95% search speed | High infrastructure cost |
| **CDN Integration** | Static asset optimization | 30-50% page load | Low implementation cost |

---

## Export & Integration Capabilities

### Current Export Features
- **CSV Export:** Client-side table data extraction
- **Basic formatting:** Standard comma-separated values
- **Date stamping:** Automatic filename generation

### Enhanced Export Recommendations
| Export Type | Format | Features | Business Use Case |
|-------------|--------|----------|-------------------|
| **Excel Enhanced** | .xlsx | Charts, pivot tables, formatting | Executive reporting |
| **PDF Professional** | .pdf | Branded templates, signatures | Client presentations |
| **API Integration** | JSON/XML | Real-time data feeds | System integration |
| **Data Warehouse** | ETL pipeline | Automated scheduling | Analytics platform |
| **Business Intelligence** | BI tools | Interactive dashboards | Strategic analysis |

### Real-time Integration Capabilities
```python
# Proposed API structure
class CDRIntegration:
    def real_time_stream(self, filters):
        """Stream CDR data in real-time"""
        return {
            'endpoint': '/api/cdr/stream',
            'method': 'WebSocket',
            'authentication': 'JWT',
            'rate_limit': '1000_requests_per_minute'
        }
    
    def batch_export(self, date_range, format):
        """Scheduled batch exports"""
        return {
            'endpoint': '/api/cdr/export',
            'formats': ['csv', 'json', 'parquet'],
            'compression': ['gzip', 'zip'],
            'delivery': ['download', 'ftp', 'email', 's3']
        }
```

---

## Security & Compliance Framework

### Data Privacy Implementation
| Privacy Aspect | Current Status | Enhancement Needed | Compliance Impact |
|----------------|----------------|-------------------|-------------------|
| **PII Masking** | Limited | Phone number masking | GDPR/CCPA compliance |
| **Access Control** | Basic | Role-based permissions | Security audit |
| **Audit Logging** | None | Complete access tracking | Compliance reporting |
| **Data Retention** | Manual | Automated lifecycle | Storage optimization |
| **Encryption** | Basic | End-to-end encryption | Data protection |

### Compliance Monitoring
```sql
-- Proposed compliance tracking
CREATE TABLE cdr_access_log (
    access_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id VARCHAR(20),
    access_timestamp DATETIME,
    filters_applied JSON,
    records_accessed INT,
    export_performed BOOLEAN,
    ip_address VARCHAR(45),
    compliance_flags JSON
);
```

This comprehensive documentation establishes the foundation for transforming the Call Detail Report into a sophisticated analytics platform with enterprise-grade capabilities and comprehensive KPI tracking.
