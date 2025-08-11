# VICIdial Enhanced Reporting System

A comprehensive PHP-based reporting system for VICIdial call centers that provides real-time monitoring, performance analytics, and business intelligence dashboards. Complete call center analytics solution with 12 specialized report modules covering all aspects of call center operations.

**Developed by Gventure Technology Pvt Ltd**  
*Professional Call Center Solutions & VICIdial Services*

## 📊 Features

### 1. Dashboard Overview (`index.php`)
- Real-time summary statistics
- Active agents, total calls, queue length
- Top performing agents and campaigns
- Quick access to all reports
- Auto-refresh functionality

### 2. Real-Time Supervisor Dashboard (`realtime_dashboard.php`)
- Live agent status monitoring
- Queue management and wait times
- Real-time call activity
- System performance metrics
- Auto-refresh every 10 seconds
- Agent status breakdown (Ready, On Call, Paused)

### 3. Disposition Effectiveness Report (`disposition_report.php`)
- Call outcome analysis
- Disposition distribution charts
- Campaign-wise disposition breakdown
- Agent performance by disposition
- Conversion rate tracking
- Effectiveness scoring system

### 4. Agent Productivity Tracker (`agent_productivity.php`)
- Individual agent performance metrics
- Talk time, pause time analysis
- Hourly productivity trends
- Conversion rates and efficiency scores
- Session duration tracking
- Performance ranking system

### 5. Campaign ROI Analysis (`campaign_roi.php`)
- Campaign performance comparison
- Sales and conversion tracking
- Agent performance by campaign
- ROI calculations and cost analysis
- Revenue tracking and profitability metrics

### 6. Performance Heatmaps (`heatmaps.php`)
- Visual heat maps for call volumes
- Peak performance hour identification
- Day/hour activity patterns
- Sales performance visualization
- SLA breach tracking
- Performance pattern analysis

### 7. Call Volume Analysis (`call_volume_report.php`)
- Comprehensive call volume tracking
- Hourly, daily, and weekly call patterns
- Peak hour identification and analysis
- Campaign-wise call distribution
- Volume trend analysis and forecasting
- Capacity planning insights

### 8. Queue Performance & SLA Report (`queue_performance_report.php`)
- Service Level Agreement (SLA) monitoring
- Queue wait time analysis
- Call abandonment rate tracking
- Agent availability correlation
- SLA compliance scoring
- Queue health metrics

### 9. Lead Management & Lifecycle Report (`lead_management_report.php`)
- Lead status distribution analysis
- Conversion funnel tracking
- Lead source performance comparison
- Lead lifecycle and aging analysis
- Campaign lead quality assessment
- Compliance and freshness monitoring

### 10. Agent Schedule Adherence Report (`agent_schedule_report.php`)
- Login/logout pattern analysis
- Break compliance monitoring
- Schedule adherence percentage
- Productivity correlation tracking
- Overtime and efficiency analysis
- Hourly availability heatmaps

### 11. Revenue & Sales Performance Report (`revenue_sales_report.php`)
- Sales trend analysis and forecasting
- Agent sales rankings and performance
- Campaign revenue and profitability
- Sales velocity tracking
- Commission and compensation metrics
- Revenue goal tracking

### 12. Compliance & Regulatory Report (`compliance_report.php`)
- Do Not Call (DNC) compliance monitoring
- Callback compliance tracking
- Call time regulation compliance
- Recording and retention compliance
- Violation analysis and reporting
- Regulatory adherence scoring

## 🚀 Installation

### Prerequisites
- PHP 7.4 or higher
- MySQL/MariaDB database
- VICIdial installation with database access
- Web server (Apache/Nginx)

### Setup Instructions

1. **Clone or Download**
   ```bash
   git clone <repository-url>
   cd vicidial-report
   ```

2. **Database Configuration**
   Edit `config/database.php`:
   ```php
   define('DB_HOST', 'localhost');     // Your VICIdial DB host
   define('DB_NAME', 'asterisk');      // VICIdial database name
   define('DB_USER', 'your_username'); // Database username
   define('DB_PASS', 'your_password'); // Database password
   ```

3. **Web Server Setup**
   - Place files in your web server document root
   - Ensure PHP has read access to all files
   - Configure virtual host if needed

4. **Permissions**
   ```bash
   chmod 644 *.php
   chmod 644 config/*.php
   chmod 644 includes/*.php
   ```

5. **Access the System**
   Open your browser and navigate to: `http://your-server/vicidial-report/`

## 📋 Database Tables Used

The system reads from these VICIdial tables:
- `vicidial_log` - Call logs and statistics
- `vicidial_live_agents` - Real-time agent status
- `vicidial_users` - Agent information
- `vicidial_campaigns` - Campaign details
- `vicidial_closer_log` - Inbound call logs
- `vicidial_auto_calls` - Active call queue
- `vicidial_agent_log` - Agent activity logs
- `vicidial_list` - Lead information and status tracking
- `vicidial_dnc` - Do Not Call registry for compliance
- `call_log` - Detailed call records and queue data
- `vicidial_callbacks` - Scheduled callback information
- `vicidial_lead_recycle` - Lead recycling and aging data

## 🎨 Features Overview

### Modern UI Components
- Bootstrap 5 responsive design
- FontAwesome icons
- Chart.js for interactive charts
- Real-time data updates
- Mobile-friendly interface

### Analytics & Insights
- Performance trending and forecasting
- Conversion rate and sales analysis
- Peak time identification and capacity planning
- SLA monitoring and compliance tracking
- Agent efficiency and productivity analysis
- Lead lifecycle and conversion funnel tracking
- Schedule adherence and attendance monitoring
- Revenue tracking and ROI analysis
- Regulatory compliance and violation monitoring
- Queue performance and wait time analysis
- Call volume patterns and distribution
- Heatmap visualizations for performance patterns

### Filtering & Customization
- Date range selection and time period analysis
- Campaign filtering and comparison
- Agent-specific views and performance tracking
- Metric-specific analysis and drilling down
- Export capabilities and data portability
- Real-time and historical data views
- Customizable dashboard layouts
- Interactive chart filtering and zooming

## 📊 Report Details

### Dashboard Metrics
- **Active Agents**: Currently logged in agents
- **Total Calls**: Daily call volume
- **Queue Length**: Calls waiting in queue
- **Average Hold Time**: Customer wait times

### Real-Time Monitoring
- **Agent Status**: Live status of all agents
- **Queue Statistics**: Per-campaign queue metrics
- **System Performance**: Overall system health
- **Recent Activity**: Latest call activities

### Performance Analytics
- **Conversion Rates**: Sales effectiveness and lead conversion
- **Talk Time Analysis**: Agent efficiency and productivity
- **Peak Hours**: Optimal staffing and capacity planning
- **Drop Patterns**: Service quality and abandonment metrics
- **Schedule Adherence**: Agent attendance and compliance
- **Revenue Tracking**: Sales performance and profitability
- **Lead Management**: Lead lifecycle and conversion funnels
- **SLA Compliance**: Service level agreement monitoring
- **Call Volume Patterns**: Traffic analysis and forecasting
- **Regulatory Compliance**: DNC and violation tracking

### Comprehensive Coverage
- **12 Specialized Reports**: Complete call center analytics
- **50+ Key Metrics**: Detailed performance indicators
- **Real-time Updates**: Live monitoring capabilities
- **Interactive Charts**: Visual data representation
- **Compliance Tracking**: Regulatory adherence monitoring
- **Forecasting Tools**: Predictive analytics and planning

## 🔧 Customization

### Adding New Reports
1. Create new PHP file in root directory
2. Follow the existing structure:
   ```php
   <?php
   $page_title = "Your Report Title";
   require_once 'config/database.php';
   require_once 'includes/header.php';
   // Your report logic here
   require_once 'includes/footer.php';
   ?>
   ```

### Modifying Queries
- All database queries are in individual report files
- Use prepared statements for security
- Add proper error handling

### Styling Changes
- Main styles are in `includes/header.php`
- Bootstrap classes for responsive design
- Custom CSS can be added to individual pages

## 🛡️ Security Considerations

- Uses PDO with prepared statements
- Input sanitization with `htmlspecialchars()`
- Database credentials in separate config file
- No direct SQL injection vulnerabilities

## 📈 Performance Tips

1. **Database Optimization**
   - Add indexes on frequently queried columns
   - Consider date partitioning for large tables
   - Regular database maintenance

2. **Caching**
   - Implement Redis/Memcached for frequently accessed data
   - Cache expensive query results
   - Use browser caching for static assets

3. **Server Configuration**
   - Enable PHP OPcache
   - Optimize MySQL configuration
   - Use SSD storage for better I/O

## 🐛 Troubleshooting

### Common Issues

1. **Database Connection Errors**
   - Verify credentials in `config/database.php`
   - Check MySQL service status
   - Ensure proper firewall settings

2. **Empty Data**
   - Verify VICIdial is generating data
   - Check date filters
   - Confirm table names match your VICIdial version

3. **Performance Issues**
   - Add database indexes
   - Optimize queries for large datasets
   - Consider pagination for large result sets

## 📞 Support

For issues or questions:
1. Check the troubleshooting section
2. Verify your VICIdial database structure
3. Review PHP error logs
4. Ensure all dependencies are met

### Professional Support
For professional installation, customization, or support services, contact:
**Gventure Technology Pvt Ltd**  
*Professional Call Center Solutions & VICIdial Services*

## 📝 License

This project is developed by Gventure Technology Pvt Ltd and is provided for educational and business use. Modify as needed for your specific VICIdial installation.

### Usage Rights
- Free for personal and commercial use
- Modification and customization allowed
- Redistribution permitted with attribution
- No warranty provided - use at your own risk

### Attribution
When redistributing or modifying this software, please maintain attribution to:
**Gventure Technology Pvt Ltd** - Original developers and maintainers

## 🔄 Updates

To update the system:
1. Backup your current installation
2. Update the files
3. Check for any database schema changes
4. Test thoroughly before production use

## 📖 Additional Resources

### VICIdial Installation Guide
Need to install VICIdial first? Check out our comprehensive installation guide:
- **CentOS 7 Installation**: `vicidial-centos7.html` - Complete step-by-step instructions
- **Ubuntu Installation**: `vicidial-ubuntu.html` - Ubuntu 22.04 LTS setup guide

### Complete System Documentation
- **Project Documentation**: `vicidial_report.html` - Comprehensive system overview and technical details

---

## 🏢 About

**Developed by Gventure Technology Pvt Ltd**  
*Professional Call Center Solutions & VICIdial Services*

This comprehensive reporting system provides complete analytics coverage for VICIdial call centers with 12 specialized report modules, 50+ key performance indicators, and real-time monitoring capabilities.

---

**Note**: This reporting system is designed for VICIdial environments. Ensure you have proper access to your VICIdial database and understand your call center's data structure before implementing. 