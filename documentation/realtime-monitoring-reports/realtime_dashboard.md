# Real-time Dashboard Documentation

## Overview
**File:** `realtime_dashboard.php`  
**Purpose:** Live monitoring of call center operations with real-time agent status, queue management, and system performance tracking.  
**Category:** Real-time Monitoring Reports  
**Access Level:** Supervisor/Manager/Operations  
**Refresh Rate:** Auto-refresh every 10 seconds  

---

## Current Fields & Data Structure

### System Metrics (Top Level KPIs)
| Field | Database Source | Type | Description | Current Display | Real-time KPI Value |
|-------|----------------|------|-------------|-----------------|-------------------|
| **Agents On Call** | `COUNT(*) FROM vicidial_live_agents WHERE status='INCALL'` | INT | Currently active calls | Stat card | ✓ Immediate capacity indicator |
| **Agents Ready** | `COUNT(*) FROM vicidial_live_agents WHERE status='READY'` | INT | Available agents | Stat card | ✓ Available capacity |
| **Agents Paused** | `COUNT(*) FROM vicidial_live_agents WHERE status='PAUSED'` | INT | Agents on break/pause | Stat card | ✓ Operational efficiency |
| **Active Calls** | `COUNT(*) FROM vicidial_auto_calls WHERE status='LIVE'` | INT | System call volume | Stat card | ✓ System load indicator |
| **Average Answer Time** | `AVG(queue_seconds) FROM vicidial_closer_log` | DECIMAL | Mean response time | HH:MM:SS | ✓ Service level performance |

### Live Agent Status Fields
| Field | Database Source | Type | Description | Current Use | Enhanced KPI Potential |
|-------|----------------|------|-------------|-------------|----------------------|
| **Agent ID** | `vla.user` | VARCHAR(20) | Agent login identifier | Primary identification | ✓ Performance correlation |
| **Agent Name** | `vu.full_name` | VARCHAR(100) | Human-readable name | Display enhancement | ✓ Team management |
| **Status** | `vla.status` | VARCHAR(20) | Current agent state | Color-coded badges | ✓ Productivity analysis |
| **Campaign** | `vla.campaign_id` | VARCHAR(8) | Active campaign assignment | Context information | ✓ Campaign workload distribution |
| **Extension** | `vla.extension` | VARCHAR(20) | Phone extension | Communication routing | ✓ Technical troubleshooting |
| **Time in Status** | `TIMEDIFF(NOW(), last_state_change)` | TIME | Duration in current state | Efficiency tracking | ✓ State transition analysis |
| **Time Since Last Call** | `TIMEDIFF(NOW(), last_call_time)` | TIME | Idle time measurement | Activity monitoring | ✓ Productivity optimization |
| **Caller ID** | `vla.callerid` | VARCHAR(18) | Current call identifier | Call tracking | ✓ Call correlation |
| **Server IP** | `vla.server_ip` | VARCHAR(15) | Server assignment | System distribution | ✓ Load balancing |
| **Pause Code** | `vla.pause_code` | VARCHAR(6) | Reason for pause status | Break categorization | ✓ Break pattern analysis |
| **Preview Lead ID** | `vla.preview_lead_id` | INT | Next lead in queue | Workflow monitoring | ✓ Lead flow optimization |
| **User Level** | `vu.user_level` | INT | Permission/experience level | Access control | ✓ Skill-based routing |

### Queue Status Information
| Field | Database Source | Type | Description | Current Display | Operational KPI |
|-------|----------------|------|-------------|-----------------|----------------|
| **Campaign ID** | `campaign_id` | VARCHAR(8) | Queue identifier | Campaign grouping | ✓ Campaign performance |
| **Calls Waiting** | `COUNT(*) WHERE status='LIVE'` | INT | Queue depth | Alert-level display | ✓ Service level monitoring |
| **Average Wait Time** | `AVG(queue_seconds)` | INT | Mean hold time | Time format | ✓ Customer experience |
| **Maximum Wait Time** | `MAX(queue_seconds)` | INT | Longest wait | Critical alerts | ✓ SLA compliance |
| **Minimum Wait Time** | `MIN(queue_seconds)` | INT | Shortest wait | Baseline measurement | ✓ System efficiency |

### Recent Activity Tracking
| Field | Database Source | Type | Description | Current Use | Analytics Value |
|-------|----------------|------|-------------|-------------|----------------|
| **Call Unique ID** | `vl.uniqueid` | VARCHAR(20) | System call identifier | Activity correlation | ✓ Call flow analysis |
| **Call Timestamp** | `vl.call_date` | DATETIME | Call occurrence time | Timeline display | ✓ Activity pattern analysis |
| **Agent** | `vl.user` | VARCHAR(20) | Handling agent | Performance attribution | ✓ Agent activity tracking |
| **Campaign** | `vl.campaign_id` | VARCHAR(8) | Campaign context | Activity categorization | ✓ Campaign activity monitoring |
| **Phone Number** | `vl.phone_number` | VARCHAR(18) | Customer contact | Customer identification | ✓ Contact frequency analysis |
| **Disposition** | `vl.status` | VARCHAR(6) | Call outcome | Success tracking | ✓ Real-time conversion rates |
| **Duration** | `vl.length_in_sec` | INT | Call length | Efficiency measurement | ✓ Productivity indicators |
| **Hangup Cause** | `vl.hangup_cause` | VARCHAR(10) | Technical termination reason | System monitoring | ✓ Technical performance |
| **Term Reason** | `vl.term_reason` | VARCHAR(30) | Business termination reason | Process monitoring | ✓ Process optimization |

### Inbound Campaign Activity
| Field | Database Source | Type | Description | Hourly Tracking | Performance KPI |
|-------|----------------|------|-------------|-----------------|----------------|
| **Campaign ID** | `vcl.campaign_id` | VARCHAR(8) | Inbound campaign | Activity grouping | ✓ Channel performance |
| **Total Calls** | `COUNT(*)` | INT | Hourly call volume | Volume monitoring | ✓ Capacity planning |
| **Queued Calls** | `COUNT(WHERE status='QUEUE')` | INT | Waiting calls | Service level | ✓ Queue management |
| **Live Calls** | `COUNT(WHERE status='LIVE')` | INT | Active conversations | Real-time capacity | ✓ Agent utilization |
| **Average Queue Time** | `AVG(queue_seconds)` | DECIMAL | Mean wait time | Service quality | ✓ Customer satisfaction |
| **Queue Ratio** | `(queued/total)*100` | DECIMAL | Percentage in queue | Efficiency indicator | ✓ System performance |

---

## Database Schema & Live Data Sources

### Primary Live Tables
```sql
-- Real-time agent status
vicidial_live_agents (vla) - CORE REAL-TIME TABLE
├── user (VARCHAR(20)) - Agent identifier [PRIMARY KEY]
├── server_ip (VARCHAR(15)) - Server assignment
├── conf_exten (VARCHAR(20)) - Conference extension
├── status (VARCHAR(20)) - Current agent state [CRITICAL]
├── lead_id (INT) - Current lead
├── campaign_id (VARCHAR(8)) - Active campaign
├── uniqueid (VARCHAR(20)) - Current call ID
├── callerid (VARCHAR(18)) - Caller identification
├── channel (VARCHAR(100)) - Telephony channel
├── random_id (INT) - Session identifier
├── last_call_time (DATETIME) - Last call timestamp
├── last_update_time (DATETIME) - Status update time
├── last_state_change (DATETIME) - State change timestamp
├── pause_epoch (INT) - Pause start time
├── pause_sec (INT) - Pause duration
├── pause_type (VARCHAR(10)) - Pause classification
├── pause_code (VARCHAR(6)) - Specific pause reason
├── preview_lead_id (INT) - Next lead preview
├── on_hook_agent (ENUM('Y','N')) - Phone status
├── extension (VARCHAR(20)) - Agent extension
├── call_server_ip (VARCHAR(15)) - Call server
└── user_level (INT) - Agent permission level

-- Active call queue
vicidial_auto_calls (vac) - QUEUE MANAGEMENT
├── auto_call_id (INT) - Queue entry ID [PRIMARY KEY]
├── server_ip (VARCHAR(15)) - Server processing
├── campaign_id (VARCHAR(8)) - Campaign assignment
├── status (VARCHAR(20)) - Queue status [LIVE/QUEUE/etc]
├── lead_id (INT) - Lead reference
├── uniqueid (VARCHAR(20)) - Call identifier
├── callerid (VARCHAR(18)) - Caller ID
├── channel (VARCHAR(100)) - Telephony channel
├── phone_code (INT) - Geographic code
├── phone_number (VARCHAR(18)) - Target number
├── call_time (DATETIME) - Scheduled time
├── call_type (VARCHAR(10)) - IN/OUT designation
├── stage (VARCHAR(20)) - Processing stage
├── last_update_time (TIMESTAMP) - Update tracking
├── alt_dial (VARCHAR(10)) - Alternative dialing
├── queue_seconds (INT) - Wait time accumulator
├── queue_position (INT) - Position in queue
└── agent_only (VARCHAR(20)) - Agent restriction

-- Inbound call tracking
vicidial_closer_log (vcl) - INBOUND ANALYTICS
├── closecallid (INT) - Log entry ID [PRIMARY KEY]
├── lead_id (INT) - Lead reference
├── list_id (INT) - List assignment
├── campaign_id (VARCHAR(8)) - Campaign context
├── call_date (DATETIME) - Call timestamp
├── start_epoch (INT) - Call start time
├── end_epoch (INT) - Call end time
├── length_in_sec (INT) - Call duration
├── status (VARCHAR(6)) - Call disposition
├── phone_code (INT) - Geographic identifier
├── phone_number (VARCHAR(18)) - Customer phone
├── user (VARCHAR(20)) - Handling agent
├── comments (VARCHAR(255)) - Agent notes
├── processed (ENUM('Y','N')) - Processing status
├── queue_seconds (INT) - Wait time before answer
├── talk_sec (INT) - Actual talk time
├── dead_sec (INT) - Dead air time
└── uniqueid (VARCHAR(20)) - System call ID
```

### Supporting Reference Tables
```sql
-- Agent master data with real-time relevance
vicidial_users (vu)
├── user (VARCHAR(20)) - Agent login [PRIMARY KEY]
├── full_name (VARCHAR(100)) - Display name
├── user_level (INT) - Permission/skill level
├── user_group (VARCHAR(20)) - Team assignment
├── phone_login (VARCHAR(20)) - Phone extension
├── phone_pass (VARCHAR(10)) - Authentication
├── active (ENUM('Y','N')) - Account status
├── modify_date (DATETIME) - Last modification
└── agent_choose_ingroups (ENUM('0','1')) - Skill routing

-- Recent call history for activity feed
vicidial_log (vl) - RECENT ACTIVITY SOURCE
├── uniqueid (VARCHAR(20)) - Call identifier
├── call_date (DATETIME) - Call timestamp [INDEXED for recent data]
├── user (VARCHAR(20)) - Agent reference
├── campaign_id (VARCHAR(8)) - Campaign reference
├── phone_number (VARCHAR(18)) - Customer phone
├── status (VARCHAR(6)) - Call disposition
├── length_in_sec (INT) - Call duration
├── hangup_cause (VARCHAR(10)) - Technical reason
├── term_reason (VARCHAR(30)) - Business reason
└── lead_id (INT) - Lead reference
```

---

## Real-time KPI Framework

### Immediate Response KPIs (10-second intervals)
| KPI Category | Metric | Calculation | Alert Threshold | Business Impact |
|--------------|--------|-------------|-----------------|-----------------|
| **Capacity Management** | Agent Availability Ratio | `ready_agents / total_logged_agents * 100` | <60% | Immediate staffing needs |
| **Service Level** | Average Speed of Answer | `AVG(queue_seconds)` | >30 seconds | Customer satisfaction |
| **Queue Health** | Maximum Wait Time | `MAX(queue_seconds)` | >120 seconds | Service level breach |
| **System Load** | Calls per Available Agent | `active_calls / ready_agents` | >1.5 | System overload |
| **Operational Efficiency** | Idle Agent Percentage | `paused_agents / total_agents * 100` | >25% | Productivity concern |

### Trend Analysis KPIs (Minute-by-minute)
| KPI Category | Metric | Calculation Method | Trend Indicator | Predictive Value |
|--------------|--------|-------------------|-----------------|------------------|
| **Volume Trending** | Call Volume Velocity | `calls_this_minute - calls_last_minute` | Increasing/Decreasing | Staffing prediction |
| **Performance Trending** | Conversion Rate Trend | `sales_last_10_min / calls_last_10_min` | Performance direction | Quality indication |
| **Queue Trending** | Queue Growth Rate | `queue_size_now - queue_size_1min_ago` | Queue building/clearing | Resource allocation |
| **Agent Productivity** | Status Change Frequency | `status_changes_per_agent_per_hour` | Stability indicator | Training needs |

### Advanced Real-time Analytics
```sql
-- Real-time performance calculation example
CREATE VIEW real_time_performance AS
SELECT 
  -- Current capacity metrics
  (SELECT COUNT(*) FROM vicidial_live_agents WHERE status = 'READY') as available_agents,
  (SELECT COUNT(*) FROM vicidial_live_agents WHERE status = 'INCALL') as busy_agents,
  (SELECT COUNT(*) FROM vicidial_auto_calls WHERE status = 'LIVE') as queued_calls,
  
  -- Service level calculations
  (SELECT AVG(queue_seconds) FROM vicidial_closer_log 
   WHERE call_date >= DATE_SUB(NOW(), INTERVAL 5 MINUTE)) as avg_answer_speed_5min,
  
  -- Productivity indicators
  (SELECT COUNT(*) FROM vicidial_log 
   WHERE call_date >= DATE_SUB(NOW(), INTERVAL 1 HOUR) 
   AND status = 'SALE') as sales_last_hour,
   
  -- System health
  (SELECT COUNT(DISTINCT server_ip) FROM vicidial_live_agents) as active_servers,
  
  -- Performance ratios
  CASE 
    WHEN (SELECT COUNT(*) FROM vicidial_live_agents WHERE status = 'READY') > 0
    THEN (SELECT COUNT(*) FROM vicidial_auto_calls WHERE status = 'LIVE') / 
         (SELECT COUNT(*) FROM vicidial_live_agents WHERE status = 'READY')
    ELSE 0
  END as calls_per_available_agent;
```

---

## Enhanced Real-time Features (Future Implementation)

### Immediate Enhancement Opportunities
| Feature | Implementation | Data Source | KPI Enhancement | Priority |
|---------|----------------|-------------|-----------------|----------|
| **Predictive Queue Alerts** | Machine learning model | Historical queue patterns | ✓ Proactive management | HIGH |
| **Agent Performance Scoring** | Real-time calculation | Live agent data + recent performance | ✓ Immediate coaching | HIGH |
| **Campaign Health Monitoring** | Traffic light system | Campaign-specific metrics | ✓ Campaign optimization | HIGH |
| **Customer Wait Time Prediction** | Algorithm-based | Queue analytics + historical data | ✓ Customer experience | MEDIUM |
| **Automatic Escalation Alerts** | Rule-based system | Threshold monitoring | ✓ Issue resolution | MEDIUM |
| **Break Pattern Analysis** | Behavioral tracking | Pause code analytics | ✓ Operational efficiency | MEDIUM |
| **Server Load Balancing** | Real-time distribution | System performance data | ✓ Technical optimization | LOW |
| **Skill-based Routing Visualization** | Dynamic mapping | Agent skills + campaign requirements | ✓ Efficiency improvement | LOW |

### Advanced Monitoring Capabilities
| Capability | Technology | Implementation | Business Value |
|------------|------------|----------------|----------------|
| **Voice Analytics Integration** | Real-time speech processing | API integration | Customer sentiment monitoring |
| **AI-powered Anomaly Detection** | Machine learning | Pattern recognition | Proactive issue identification |
| **Predictive Staffing** | Forecasting algorithms | Historical + real-time data | Optimal resource allocation |
| **Dynamic Goal Setting** | Adaptive algorithms | Performance analytics | Continuous improvement |
| **Integrated Communication** | Unified platform | Multi-channel integration | Comprehensive monitoring |

### Real-time Data Enhancements
| Data Enhancement | Source | Type | Real-time Value | Implementation Complexity |
|------------------|--------|------|-----------------|--------------------------|
| **Customer Journey Stage** | CRM integration | VARCHAR(50) | Personalized handling | Medium |
| **Agent Mood/Stress Level** | Voice biometrics | DECIMAL(3,1) | Performance correlation | High |
| **Network Quality Metrics** | VoIP monitoring | JSON | Call quality prediction | Medium |
| **Customer Satisfaction Score** | Real-time surveys | DECIMAL(3,1) | Immediate feedback | Low |
| **Revenue per Call** | Transaction systems | DECIMAL(10,2) | Economic efficiency | Medium |
| **Lead Quality Score** | ML scoring | INT(1-100) | Prioritization optimization | High |
| **Competitive Intelligence** | Market data feeds | JSON | Strategic positioning | High |
| **Weather/Events Impact** | External APIs | JSON | External factor correlation | Low |

---

## Alert & Notification Framework

### Current Alert Thresholds
| Alert Type | Condition | Current Response | Enhanced Response Needed |
|------------|-----------|------------------|-------------------------|
| **High Queue Volume** | >10 calls waiting | Visual indicator | Automated escalation |
| **Long Wait Times** | >2 minutes average | Color coding | Manager notification |
| **Agent Unavailability** | <3 ready agents | Dashboard display | Staffing alerts |
| **System Performance** | Server issues | Manual monitoring | Automated alerts |

### Enhanced Alert System
```javascript
// Proposed alert configuration
const alertSystem = {
  serviceLevel: {
    metric: 'average_answer_time',
    thresholds: {
      warning: 30,    // seconds
      critical: 60,   // seconds
      emergency: 120  // seconds
    },
    actions: {
      warning: ['dashboard_highlight', 'supervisor_notification'],
      critical: ['email_alert', 'sms_notification', 'escalation_queue'],
      emergency: ['conference_call', 'executive_notification', 'emergency_protocol']
    }
  },
  
  agentPerformance: {
    metric: 'conversion_rate_deviation',
    calculation: 'current_rate - rolling_average',
    thresholds: {
      underperforming: -5,  // percentage points
      coaching_needed: -10,
      intervention_required: -15
    },
    actions: {
      underperforming: ['performance_flag', 'supervisor_dashboard'],
      coaching_needed: ['coaching_alert', 'training_recommendation'],
      intervention_required: ['immediate_supervision', 'quality_review']
    }
  },
  
  systemHealth: {
    metrics: ['server_response_time', 'call_quality', 'network_latency'],
    monitoring: 'continuous',
    escalation: 'automatic'
  }
}
```

---

## Performance Optimization & Scalability

### Current Performance Considerations
1. **Auto-refresh Impact:** 10-second intervals for all users
2. **Database Load:** Multiple concurrent real-time queries
3. **Browser Performance:** JavaScript timer management
4. **Network Bandwidth:** Continuous data transfer

### Optimization Recommendations
| Optimization Type | Implementation | Performance Gain | Resource Impact |
|------------------|----------------|------------------|-----------------|
| **WebSocket Implementation** | Real-time bidirectional communication | 70-80% bandwidth reduction | Low CPU overhead |
| **Data Compression** | GZIP compression for data transfer | 60-70% bandwidth savings | Minimal CPU impact |
| **Selective Updates** | Only changed data transmission | 80-90% data reduction | Medium implementation effort |
| **Client-side Caching** | Browser-based data storage | 50-60% server load reduction | Low memory usage |
| **Database Connection Pooling** | Shared database connections | 40-50% database load reduction | Medium memory usage |
| **CDN Integration** | Static asset optimization | 30-40% page load improvement | Low infrastructure cost |

### Scalability Architecture
```python
# Proposed scalable architecture
class RealtimeDashboard:
    def __init__(self):
        self.websocket_connections = {}
        self.data_cache = RedisCache()
        self.event_queue = MessageQueue()
        
    def handle_live_data(self):
        """Process real-time data updates"""
        return {
            'data_sources': [
                'vicidial_live_agents',
                'vicidial_auto_calls', 
                'vicidial_closer_log'
            ],
            'update_frequency': '5_seconds',
            'compression': 'gzip',
            'delivery_method': 'websocket_push'
        }
    
    def optimize_queries(self):
        """Database optimization strategies"""
        return {
            'connection_pooling': True,
            'query_caching': '30_seconds',
            'index_optimization': 'automatic',
            'partition_strategy': 'time_based'
        }
```

---

## Integration & API Development

### Current Integration Status
- **Internal:** Direct database access
- **External:** Limited integration capabilities
- **API:** No formal API structure
- **Mobile:** Desktop-only interface

### Proposed Integration Framework
| Integration Type | Method | Data Format | Use Case | Priority |
|------------------|--------|-------------|----------|----------|
| **Mobile Apps** | REST API | JSON | Mobile supervision | HIGH |
| **External Dashboards** | WebSocket API | Real-time streams | Executive dashboards | HIGH |
| **BI Systems** | ETL pipeline | Structured data | Analytics platforms | MEDIUM |
| **Notification Systems** | Webhook | Event-driven | Alert management | MEDIUM |
| **Voice Analytics** | Real-time API | Audio streams | Quality monitoring | LOW |
| **CRM Integration** | Bidirectional API | Customer data | 360° view | LOW |

This comprehensive documentation provides the foundation for evolving the real-time dashboard into a sophisticated, scalable monitoring and management platform with predictive capabilities and enterprise-grade performance.
