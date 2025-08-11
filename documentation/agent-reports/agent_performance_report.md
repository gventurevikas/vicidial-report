# Agent Performance Report Documentation

## Overview
**File:** `agent_performance_report.php`  
**Purpose:** Comprehensive performance analysis for agents including KPIs, productivity metrics, and session tracking with advanced analytics.  
**Category:** Agent Reports  
**Access Level:** Manager/Admin  

---

## Current Fields & Data Structure

### Input Parameters (Filters)
| Field | Type | Source | Description | Validation |
|-------|------|--------|-------------|------------|
| `start_date` | DATE | GET | Performance analysis start date | Default: 30 days ago |
| `end_date` | DATE | GET | Performance analysis end date | Default: Today |
| `agent` | VARCHAR | GET | Specific agent filter | Optional, from `vicidial_users` |
| `campaign` | VARCHAR | GET | Campaign-specific analysis | Optional, from `vicidial_campaigns` |
| `debug` | BOOLEAN | GET | Debug mode toggle | Development/troubleshooting |

### Performance Overview Fields
| Field | Database Source | Type | Description | Current KPI | Future KPI Potential |
|-------|----------------|------|-------------|-------------|---------------------|
| **Agent ID** | `vl.user` | VARCHAR(20) | Agent identifier | Primary key | ✓ Cross-system integration |
| **Agent Name** | `u.full_name` | VARCHAR(100) | Agent display name | User interface | ✓ Performance leaderboards |
| **Total Calls** | `COUNT(vl.uniqueid)` | INT | Complete call volume | Volume tracking | ✓ Productivity benchmarking |
| **Sales Made** | `COUNT(CASE WHEN sale='Y')` | INT | Successful conversions | Revenue tracking | ✓ Commission calculations |
| **Contacts Made** | `COUNT(CASE WHEN status IN ('SALE','A','B','C'))` | INT | Meaningful interactions | Quality metric | ✓ Contact efficiency ratio |
| **Average Call Duration** | `ROUND(AVG(length_in_sec), 2)` | DECIMAL | Mean call length | Efficiency indicator | ✓ Optimal duration analysis |
| **Total Talk Time** | `SUM(length_in_sec)` | INT | Cumulative speaking time | Productivity measure | ✓ Utilization percentage |
| **Drops** | `COUNT(CASE WHEN status='DROP')` | INT | Disconnected calls | Quality indicator | ✓ Technical issue tracking |
| **Conversion Rate** | `(sales/contacts)*100` | DECIMAL(5,2) | Sales success percentage | Performance KPI | ✓ Skill assessment |
| **Drop Rate** | `(drops/total_calls)*100` | DECIMAL(5,2) | Call failure percentage | Quality KPI | ✓ System reliability |
| **Contact Rate** | `(contacts/total_calls)*100` | DECIMAL(5,2) | Meaningful contact percentage | Efficiency KPI | ✓ Lead quality assessment |
| **Campaigns Worked** | `COUNT(DISTINCT campaign_id)` | INT | Campaign diversity | Flexibility metric | ✓ Cross-training effectiveness |
| **Calls Per Agent** | `total_calls/agent_count` | DECIMAL | Volume distribution | Workload balance | ✓ Capacity planning |
| **Performance Period** | `MIN/MAX(call_date)` | DATE | Active date range | Activity tracking | ✓ Consistency analysis |

### Session Details Fields
| Field | Database Source | Type | Description | Current KPI | Future KPI Potential |
|-------|----------------|------|-------------|-------------|---------------------|
| **Work Date** | `DATE(event_date)` | DATE | Working day | Attendance tracking | ✓ Schedule adherence |
| **First Login** | `MIN(LOGIN event_date)` | TIME | Session start time | Punctuality metric | ✓ Schedule compliance |
| **Last Logout** | `MAX(LOGOUT event_date)` | TIME | Session end time | Shift completion | ✓ Overtime tracking |
| **Login Count** | `COUNT(LOGIN events)` | INT | Session restart frequency | System usage pattern | ✓ Technical difficulty indicator |
| **Logout Count** | `COUNT(LOGOUT events)` | INT | Session termination frequency | Completion tracking | ✓ Process adherence |
| **Session Status** | Calculated | VARCHAR(20) | Complete/Incomplete session | Workflow compliance | ✓ Operational efficiency |

---

## Database Schema Dependencies

### Primary Tables
```sql
-- Main performance data source
vicidial_log (vl)
├── uniqueid (VARCHAR(20)) - Call identifier
├── user (VARCHAR(20)) - Agent reference
├── call_date (DATETIME) - Performance timestamp
├── status (VARCHAR(6)) - Call outcome
├── length_in_sec (INT) - Talk time
├── campaign_id (VARCHAR(8)) - Campaign reference
├── phone_number (VARCHAR(18)) - Customer contact
├── lead_id (INT) - Lead reference
└── term_reason (VARCHAR(30)) - Call end reason

-- Agent session tracking
vicidial_user_log (vul) / vicidial_agent_log (val)
├── user (VARCHAR(20)) - Agent reference
├── event_date (DATETIME) - Session timestamp
├── operation (VARCHAR(20)) - LOGIN/LOGOUT/PAUSE
├── event (VARCHAR(20)) - Session event type
├── pause_sec (INT) - Pause duration
└── pause_code (VARCHAR(6)) - Pause reason

-- Agent master data
vicidial_users (u)
├── user (VARCHAR(20)) - Agent ID (PRIMARY KEY)
├── full_name (VARCHAR(100)) - Agent name
├── user_level (INT) - Permission level
├── active (ENUM('Y','N')) - Account status
├── user_group (VARCHAR(20)) - Team assignment
└── agent_choose_ingroups (ENUM('0','1')) - Skill flexibility
```

### Supporting Tables
```sql
-- Campaign configuration
vicidial_campaigns (vc)
├── campaign_id (VARCHAR(8)) - Campaign code
├── campaign_name (VARCHAR(40)) - Display name
├── active (ENUM('Y','N')) - Status
└── campaign_description (TEXT) - Details

-- Status definitions for performance categorization
vicidial_statuses (vs)
├── status (VARCHAR(6)) - Status code
├── status_name (VARCHAR(100)) - Description
├── sale (ENUM('Y','N')) - Revenue indicator
├── customer_contact (ENUM('Y','N')) - Contact indicator
└── selectable (ENUM('Y','N')) - Availability
```

---

## Current KPI Framework

### Primary Performance KPIs
| KPI Category | Metric | Calculation | Target Range | Business Impact |
|--------------|--------|-------------|--------------|-----------------|
| **Volume** | Calls per Day | Daily call count | 80-120 calls | Productivity baseline |
| **Quality** | Conversion Rate | (Sales ÷ Contacts) × 100 | 15-25% | Revenue generation |
| **Efficiency** | Contact Rate | (Contacts ÷ Total Calls) × 100 | 40-60% | Lead quality indicator |
| **Reliability** | Drop Rate | (Drops ÷ Total Calls) × 100 | <5% | System/process quality |
| **Consistency** | Daily Performance Variance | Standard deviation of daily metrics | <20% | Reliability indicator |

### Secondary Performance KPIs
| KPI Category | Metric | Calculation | Target Range | Business Impact |
|--------------|--------|-------------|--------------|-----------------|
| **Engagement** | Average Talk Time | Mean call duration | 2-5 minutes | Customer engagement |
| **Flexibility** | Campaign Diversity | Distinct campaigns worked | 2-5 campaigns | Cross-training success |
| **Attendance** | Schedule Adherence | Login punctuality percentage | >95% | Operational reliability |
| **Persistence** | Session Stability | Logins per day | 1-2 sessions | Technical proficiency |

---

## Advanced Analytics Opportunities

### 1. Predictive Performance Modeling
```sql
-- Performance trend analysis
WITH daily_performance AS (
  SELECT agent_id, 
         DATE(call_date) as work_date,
         COUNT(*) as daily_calls,
         AVG(conversion_rate) as daily_conversion,
         -- Trend calculation for forecasting
         ROW_NUMBER() OVER (PARTITION BY agent_id ORDER BY DATE(call_date)) as day_sequence
  FROM performance_data
)
SELECT agent_id,
       -- 7-day moving average for trend analysis
       AVG(daily_conversion) OVER (
         PARTITION BY agent_id 
         ORDER BY day_sequence 
         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) as conversion_trend
```

### 2. Performance Correlation Analysis
| Factor | Data Source | Correlation Analysis | Optimization Opportunity |
|--------|-------------|---------------------|-------------------------|
| **Time of Day** | `HOUR(call_date)` | Performance by hour | Optimal scheduling |
| **Day of Week** | `DAYNAME(call_date)` | Weekly patterns | Workload distribution |
| **Campaign Type** | `campaign_characteristics` | Skill matching | Specialized training |
| **Lead Quality** | `lead_scoring_system` | Success prediction | Lead routing optimization |
| **Agent Experience** | `tenure_calculation` | Learning curve analysis | Training program effectiveness |

### 3. Real-time Performance Monitoring
```javascript
// Real-time KPI dashboard elements
const realtimeKPIs = {
  currentConversionRate: {
    calculation: "sales_today / contacts_today * 100",
    threshold: 15,
    alertLevel: "warning"
  },
  hourlyCallPace: {
    calculation: "calls_this_hour * 8", // Projected daily
    threshold: 80,
    alertLevel: "info"
  },
  qualityScore: {
    calculation: "(conversion_rate * 0.4) + (contact_rate * 0.3) + (attendance * 0.3)",
    threshold: 70,
    alertLevel: "critical"
  }
}
```

---

## Enhanced Field Recommendations

### Immediate Implementation (Phase 1)
| Field Name | Data Source | Type | Purpose | KPI Impact |
|------------|-------------|------|---------|------------|
| **Hourly Performance** | Time-bucketed aggregation | JSON | Peak performance identification | ✓ Scheduling optimization |
| **Lead Source Tracking** | `vicidial_list.source_id` | VARCHAR(10) | Source ROI analysis | ✓ Marketing effectiveness |
| **Customer Callback Rate** | `vicidial_callbacks` | DECIMAL(5,2) | Service quality indicator | ✓ Customer satisfaction |
| **Training Completion** | HR system integration | INT | Skill development tracking | ✓ Performance correlation |
| **Quality Score** | Call monitoring system | DECIMAL(3,1) | Service quality measurement | ✓ Customer experience |

### Advanced Implementation (Phase 2)
| Field Name | Data Source | Type | Purpose | KPI Impact |
|------------|-------------|------|---------|------------|
| **Customer Satisfaction** | Post-call surveys | DECIMAL(3,1) | Service quality validation | ✓ NPS correlation |
| **Revenue per Call** | Sales integration | DECIMAL(10,2) | Economic efficiency | ✓ Profitability analysis |
| **Skills Matrix Score** | Skills assessment system | INT | Capability measurement | ✓ Assignment optimization |
| **Stress Level Indicator** | Behavioral analytics | INT(1-10) | Wellness monitoring | ✓ Performance sustainability |
| **Competitive Analysis** | Market data integration | JSON | Performance benchmarking | ✓ Industry positioning |

### Future Innovation (Phase 3)
| Field Name | Data Source | Type | Purpose | KPI Impact |
|------------|-------------|------|---------|------------|
| **AI Performance Score** | Machine learning model | DECIMAL(5,2) | Predictive performance rating | ✓ Proactive management |
| **Emotional Intelligence Index** | Conversation analysis | DECIMAL(3,1) | Soft skills measurement | ✓ Customer relationship quality |
| **Fatigue Factor** | Voice/behavior analysis | DECIMAL(3,1) | Performance degradation prediction | ✓ Schedule optimization |
| **Learning Velocity** | Skill acquisition tracking | DECIMAL(5,2) | Development rate measurement | ✓ Training personalization |

---

## Advanced KPI Development

### 1. Composite Performance Scores
```sql
-- Comprehensive performance scoring algorithm
CREATE VIEW agent_performance_score AS
SELECT 
  agent_id,
  -- Volume Score (25% weight)
  (CASE 
    WHEN daily_calls >= 100 THEN 100
    WHEN daily_calls >= 80 THEN 80 + (daily_calls - 80) * 1
    ELSE daily_calls * 0.8
  END) * 0.25 as volume_score,
  
  -- Quality Score (40% weight)
  (conversion_rate * 2) * 0.40 as quality_score,
  
  -- Efficiency Score (25% weight)
  (contact_rate * 1.67) * 0.25 as efficiency_score,
  
  -- Reliability Score (10% weight)
  ((100 - drop_rate) * 1) * 0.10 as reliability_score,
  
  -- Combined Score
  ((volume_score + quality_score + efficiency_score + reliability_score)) as total_score
```

### 2. Dynamic Benchmarking
| Benchmark Type | Calculation Method | Update Frequency | Application |
|----------------|-------------------|------------------|-------------|
| **Peer Comparison** | Same-level agent averages | Daily | Relative performance |
| **Historical Performance** | Agent's own 30-day average | Weekly | Personal improvement |
| **Campaign Standards** | Campaign-specific benchmarks | Monthly | Context-appropriate goals |
| **Industry Standards** | External benchmark data | Quarterly | Competitive positioning |

### 3. Predictive Analytics KPIs
```python
# Performance prediction model components
predictive_features = {
    'trend_indicators': [
        'performance_velocity',      # Rate of improvement
        'consistency_score',         # Performance stability
        'learning_curve_position'    # Skill development stage
    ],
    'external_factors': [
        'seasonal_adjustments',      # Time-based variations
        'campaign_difficulty',       # Task complexity
        'market_conditions'          # External environment
    ],
    'behavioral_patterns': [
        'attendance_reliability',    # Schedule adherence
        'break_patterns',           # Work rhythm analysis
        'communication_quality'      # Soft skills indicators
    ]
}
```

---

## Implementation Roadmap

### Phase 1: Enhanced Data Collection (Month 1)
1. **Session Tracking Enhancement**
   - Implement detailed break code tracking
   - Add real-time performance calculations
   - Create hourly performance snapshots

2. **Quality Integration**
   - Integrate call quality scores
   - Add customer feedback mechanisms
   - Implement supervisor evaluation tracking

### Phase 2: Advanced Analytics (Month 2)
1. **Predictive Modeling**
   - Develop performance trend algorithms
   - Create early warning systems
   - Implement optimization recommendations

2. **Comparative Analysis**
   - Build peer comparison systems
   - Create team performance dashboards
   - Implement benchmark tracking

### Phase 3: AI Integration (Month 3)
1. **Machine Learning Enhancement**
   - Deploy performance prediction models
   - Implement automated coaching suggestions
   - Create personalized development plans

2. **Real-time Optimization**
   - Build dynamic workload distribution
   - Implement real-time performance alerts
   - Create adaptive target setting

---

## ROI & Business Impact

### Performance Improvement Targets
| Metric | Current Baseline | 6-Month Target | Expected ROI |
|--------|------------------|----------------|--------------|
| **Conversion Rate** | 18% | 22% | +22% revenue increase |
| **Contact Rate** | 45% | 52% | +15% efficiency gain |
| **Drop Rate** | 8% | 5% | +3% quality improvement |
| **Agent Retention** | 75% | 85% | -40% recruitment costs |
| **Training Effectiveness** | 60% | 80% | +33% skill development speed |

This comprehensive documentation provides the foundation for transforming the Agent Performance Report into a sophisticated performance management system with predictive capabilities and actionable insights.
