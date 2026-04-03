A Complete Beginner’s Guide to Application Monitoring
You’re running a busy restaurant kitchen during peak hours. Orders are flowing in, chefs are preparing multiple dishes simultaneously, and everything needs to work like a well-oiled machine.

How do you know things are running smoothly?

You watch the order times,
check food quality, monitor inventory levels,
and keep an eye on kitchen staff performance.
This is exactly what observability means for modern applications — but instead of a kitchen, we’re managing complex software systems.

In its simplest terms, observability is the ability to understand what’s happening inside your application by looking at its external outputs. Instead of waiting for problems to surface, observability lets you actively monitor, track, and understand your application’s behavior in real time.

Observability Matters Now More Than Ever.
Modern applications have evolved dramatically from their traditional counterparts. Let’s break down why observability matter;

Increased Complexity: Today’s applications aren’t simple programs running on a single server. They’re distributed across multiple services, containers, and cloud platforms. Each component needs to work perfectly with others to deliver a seamless user experience.
Speed of Change: Updates and deployments happen frequently — sometimes multiple times per day. Without proper observability, it’s impossible to ensure these changes don’t negatively impact users.
User Expectations: Modern users expect 24/7 availability and lightning-fast performance. Even minor issues can lead to lost customers and revenue.
Who Needs Observability?
Development Teams: To understand how their code performs in production
Operations Teams: To maintain system health and respond to incidents
Product Managers: To track feature usage and user experience
Business Leaders: To ensure service quality meets business objectives
Let’s expand on our restaurant kitchen analogy to understand observability components:

Press enter or click to view image in full size

How SigNoz Fits Into the Picture
SigNoz acts as your kitchen management system, providing:

Real-time monitoring of all “orders” (requests) flowing through your system
Instant alerts when “dishes” (services) aren’t performing well
Detailed insights into how different “stations” (components) work together
Historical data to help optimize your “kitchen layout” (system architecture)
Understanding Modern Application Challenges
Modern applications face unique challenges that make observability essential:

Microservices Complexity
Multiple independent services working together
Complex communication patterns
Distributed nature making issues harder to trace
Cloud Infrastructure Dynamics
Auto-scaling resources
Multiple deployment environments
Variable performance characteristics
Rapid Development Cycles
Frequent code deployments
Multiple teams working simultaneously
Continuous integration and delivery
Before diving into technical details, consider these fundamental questions:

What aspects of your application are most critical to monitor?
Who needs access to observability data?
What response times are acceptable for your use case?
How will you handle alerts and incidents?
Key Takeaways
Observability is not just monitoring — it’s about understanding your system’s behavior comprehensively.
Modern applications require observability due to their distributed nature and complexity.
Like a well-managed kitchen, good observability practices help prevent problems before they affect users.
SigNoz provides the tools needed to implement effective observability in your applications.
The Three Pillars of Observability: Metrics, Logs, and Traces
Let’s break down the three main tools you’ll use to understand your application’s health. Think of these as your application’s vital signs — just like a doctor uses temperature, blood pressure, and heart rate to check your health!

1. Metrics
Metrics are numbers that tell you how your application is doing. They’re like the speedometer and fuel gauge in your car!

Common Metrics You’ll Use
Request Rate (How many people are using your app?). SigNoz tracks
↪️Total Requests: 1,500 requests/minute
↪️Success Rate: 98.5%
↪️Error Rate: 1.5%

2. Response Time (How fast is your app?)

Average Response Time: 200ms
95th Percentile: 500ms  # Only 5% of requests are slower than this
3. Resource Usage (Is your app using too much memory/CPU?)

CPU Usage: 45%
Memory Usage: 2.5GB/8GB
When to Use Metrics
To check if your application is healthy
To spot trends (Is it getting slower?)
To know when to add more servers
To set up alerts
Real-World Example
If you run an online store, important metrics to watch are:

Number of orders per minute
Shopping cart load time
Payment success rate
Server resources during sales
2. Logs: Your Application’s Diary
What Are Logs?
Logs are your application’s diary entries. Every time something happens, your app writes it down.

Types of Logs
Info Logs; this is an entry of normal everyday activities
// Good log example
[2024-01-15 10:30:15] INFO: User 'john_doe' successfully logged in
[2024-01-15 10:30:16] INFO: Shopping cart created for session #12345
2. Error Logs: triggered when things go wrong

// Error log example
[2024-01-15 10:31:00] ERROR: Payment processing failed
Details: Transaction timeout for order #789
User Impact: Unable to complete purchase
3. Debug Logs (Detailed information for fixing problems)

// Debug log example
[2024-01-15 10:31:01] DEBUG: Payment API response
Status: 408
Request ID: pay_789
Retry attempt: 2 of 3
When to Use Logs
To understand what happened during an error
To track user actions
To debug problems
To audit security events
3. Traces
Traces are your application’s GPS, showing you the journey of a request through your application.

User Login Request (Total Time: 300ms)
├── Check Username (50ms)
├── Verify Password (100ms)
├── Get User Preferences (100ms)
│ ├── Load Settings (70ms)
│ └── Load Theme (30ms)
└── Create Session (50ms)

Use Traces when:

When your app feels slow
To find bottlenecks
To understand complex errors
To optimize performance
These 3 pillars all come together in your Signoz dashboard;
📶Key Metrics Panel

Request rates
Error rates
Response times
📶Log Viewer

Filter by severity
Search by user/session
Time-based viewing
📶Trace Explorer

View request flows
Find slow components
Debug errors
💡Pro Tip!
Start with basic metrics
Add detailed logging where needed
Use traces for performance issues
Don’t try to monitor everything at once!
Practice Exercise
Try answering these questions about your application:

How many users are active right now? (Metrics)
What errors happened in the last hour? (Logs)
Why did that last request take so long? (Traces)
Phase 1: Installating Signoz
Before You Start ✋
Make sure your system has:

At least 4GB of RAM
Docker installed
Docker Compose installed
Git installed
Ports 3301 and 3000 available
Step 1: Prepare Your System
# Check if Docker is installed
docker --version
# Should show something like: Docker version 20.10.x

# Check if Docker Compose is installed
docker-compose --version
# Should show something like: docker-compose version 1.29.x

# Create a directory for SigNoz
mkdir signoz
cd signoz
Step 2: Download SigNoz
# Clone the SigNoz repository
git clone -b main https://github.com/SigNoz/signoz.git
# Go to the deploy directory
cd signoz/deploy/
Step 3: Start SigNoz
# For Linux/MacOS
docker-compose -f docker/clickhouse-setup/docker-compose.yaml up -d

# For Windows
docker-compose -f docker\clickhouse-setup\docker-compose.yaml up -d
Step 4: Verify Installation
Open your web browser
Go to http://localhost:3301
You should see the SigNoz login page
Default credentials:
Email: admin@signoz.io
Password: admin
Step 5: Set Up Your First Application
Log in to SigNoz
Click “Add Application” button
Choose your application type:
Node.js
Python
Java
Go
Others
Step 6: Instrument Your Application
Let’s take a Python application as an example:

# Install the OpenTelemetry packages
pip install opentelemetry-api
pip install opentelemetry-sdk
pip install opentelemetry-instrumentation
pip install opentelemetry-exporter-otlp

# Add this to the start of your main.py
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Initialize tracing
trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

# Configure the exporter
otlp_exporter = OTLPSpanExporter(
    endpoint="http://localhost:4317"
)
span_processor = BatchSpanProcessor(otlp_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)
Step 7: Verify Data Collection
Run your application
Wait 2–3 minutes
Check SigNoz dashboard
You should see:
Request counts
Response times
Error rates
Step 8: Set Up Basic Monitoring
Go to “Dashboards”
Click “Create New Dashboard”
Add these essential panels:
Panel 1: Request Rate
- Metric: request_rate
- Timeframe: Last 15 minutes

Panel 2: Error Rate
- Metric: error_rate
- Timeframe: Last 15 minutes

Panel 3: Response Time
- Metric: response_time_p95
- Timeframe: Last 15 minutes
Step 9: Create Your First Alert
Go to “Alerts” section
Click “New Alert”
Configure basic error alert:
Name: High Error Rate
Metric: error_rate
Condition: > 5%
Duration: 5 minutes
Notification: Email
Step 10: Validate Setup
✅ Check these items:

Get Ifeoma Orika’s stories in your inbox
Join Medium for free to get updates from this writer.

Enter your email
Subscribe

Remember me for faster sign in

◻️SigNoz UI is accessible

◻️Application is sending data

◻️Dashboard shows metrics

◻️Test alert works

Common Problems & Solutions
Problem 1: No Data Showing

# Check if containers are running
docker ps | grep signoz

# Check container logs
docker logs signoz-otel-collector
Problem 2: Can’t Access UI

# Check if port is available
netstat -tulpn | grep 3301

# Restart the containers
docker-compose down
docker-compose up -d
Problem 3: High Memory Usage

# Check container stats
docker stats

# Adjust container memory limits in docker-compose.yaml
Phase 2: Implementing SigNoz in Your Application
Step 1: Creating Your First Dashboard
Let’s build a practical monitoring dashboard for your application.

1. Go to Dashboards → New Dashboard
2. Click "Add Panel"
3. Name your dashboard "Application Overview"
Essential Panels to Add:
Panel 1: Application Health

Metrics to Add:
- request_rate
- error_rate
- p95_latency

Panel Settings:
- Chart Type: Time Series
- Time Range: Last 15 minutes
- Refresh: Every 1 minute

Panel 2: Resource Usage

Metrics to Add:
- cpu_usage
- memory_usage
- disk_usage

Panel Settings:
- Chart Type: Gauge
- Thresholds:
Green: 0–70%
Yellow: 70–85%
Red: 85–100%

Step 2: Setting Up Essential Alerts
Let’s configure alerts for common issues.

Alert 1: High Error Rate

Go to Alerts → Create Alert
Configure:
> Name: “High Error Rate”
> Condition: error_rate > 5%
> Duration: 5 minutes
> Severity: High
> Notification: Email/Slack
Alert 2: Response Time

Create Alert
2. Configure:
> Name: “Slow Response Time”
> Condition: p95_latency > 500ms
> Duration: 3 minutes
> Severity: Medium
> Notification: Email/Slack
Step 3: Monitoring User Experience
Set up real user monitoring:

// Add to your frontend application
import { trace } from '@opentelemetry/api';

// Track page loads
trace.track('page_load', {
    page: window.location.pathname,
    loadTime: performance.now()
});

// Track user interactions
function trackClick(elementId) {
    trace.track('user_click', {
        element: elementId,
        timestamp: new Date().toISOString()
    });
}
Step 4: Setting Up Service Dependencies
Track how your services interact:

Go to “Service Map”
Click “Configure”
Add services:
— name: frontend
* type: web
— name: backend-api
* type: service
— name: database
* type: database
Step 5: Log Management
Set up structured logging:

# Python example
import logging
import json

logger = logging.getLogger('application')

def structured_log(event_type, **kwargs):
    log_entry = {
        'event': event_type,
        'timestamp': datetime.now().isoformat(),
        'data': kwargs
    }
    logger.info(json.dumps(log_entry))

# Usage
structured_log('user_action', 
    user_id='123',
    action='purchase',
    amount=99.99
)
Step 6: Performance Monitoring
Set up key performance indicators:

Backend Performance

This requires you to;

Create Dashboard Panel
Add Metrics:
— API response times
— Database query times
— Cache hit rates
— Error counts by endpoint
Frontend Performance

Add Frontend Metrics:
— Page load time
— First contentful paint
— Time to interactive
— JS errors
Step 7: Custom Metrics
Add business-specific metrics:

# Python example
from opentelemetry import metrics

meter = metrics.get_meter(__name__)

# Create counter for business metrics
order_counter = meter.create_counter(
    name="orders_processed",
    description="Number of orders processed"
)

# Usage
def process_order(order):
    # Process order logic here
    order_counter.add(1, {"status": "success"})
Step 8: Set Up Teams and Access
Organize monitoring by teams:

Create Teams:
Frontend Team
Access: Frontend metrics, User experience data
Backend Team
Access: API metrics, Database performance
DevOps Team
Access: Full system metrics
Step 9: Create Incident Response Playbooks
Set up standard responses:

High Error Rate Response:

Check error logs
2. Identify affected services
3. Check recent deployments
4. Roll back if necessary
5. Notify users if needed
Performance Degradation:

Check resource usage
Analyze slow transactions
Check database performance
Scale resources if needed
Step 10: Regular Maintenance
Set up maintenance schedules:

Daily:
- Check dashboard health
- Review error rates
- Monitor resource usage

Weekly:
- Review alert patterns
- Adjust thresholds
- Check data retention

Monthly:
- Review and optimize alerts
- Clean up unused dashboards
- Update documentation
Common Implementation Patterns
Pattern 1: Microservice Monitoring
For each service:
1. Service health dashboard
2. Service-specific alerts
3. Dependency mapping
4. Error tracking

Pattern 2: User Journey Monitoring

Track key user flows:
1. Login process
2. Checkout flow
3. Search functionality
4. Account management

Troubleshooting
Problem 1: No Data Showing Up

Step-by-Step Check:
1. Check Container Status:

$ docker ps | grep signoz
2. Verify Ports:

$ netstat -tulpn | grep 4317
3. Check Collector Logs:

$ docker logs signoz-otel-collector
Common Solutions:
- Restart the collector

$ docker restart signoz-otel-collector
- Verify instrumentation code
- Check network connectivity

Problem 2: High Memory Usage

Fix this by:
1. Check Resource Usage:
   $ docker stats

2. Adjust Memory Limits:
   Edit docker-compose.yaml:
   services:
     clickhouse:
       deploy:
         resources:
           limits:
             memory: 4G

3. Clean Old Data:
   - Go to Settings → Data Management
   - Adjust retention period
   - Run cleanup job
Problem 3: Missing Traces

Verification Steps:
1. Check sampling rate settings
2. Verify instrumentation:
— Correct endpoint configuration
— Proper SDK initialization
3. Test trace generation:

$ curl -X GET http://localhost:4317/v1/traces
Best Practices
1. Naming Conventions
For metrics:
- Use lowercase with underscores
- Include service name as prefix
- Format: service_metric_type
Example: auth_service_login_attempts

And for labels:
- Keep it simple and consistent
- Use meaningful names
- Format: category_subcategory
Example: env_production

2. Alert Configuration
A good alert structure has:
1. Clear Name:
“High Error Rate — Auth Service”

2. Precise Conditions:
condition: error_rate > 5%
duration: 5m
severity: high

3. Helpful Description:
“Auth service error rate exceeded 5% for 5 minutes”

4. Action Items:
— Check service logs
— Verify dependencies
— Contact team if needed

3. Resource Management
Memory Usage:

Recommended Limits:
- Small deployments: 2–4GB
- Medium deployments: 4–8GB
- Large deployments: 8GB+

Optimization Tips:
1. Adjust sampling rate
2. Set appropriate retention
3. Use efficient queries

Storage Planning:

Calculate Requirements:
Daily Data = (Events × Size) + (Traces × Size)
Storage = Daily Data × Retention Days

An example of this is:
- 1M events/day × 1KB = 1GB
- 30 days retention = 30GB

4. Query Optimization
Good Practices:
1. Use specific time ranges
2. Filter data effectively
3. Limit results when possible

Example Query:
- Instead of: SELECT * FROM traces
- Use: SELECT timestamp, duration, service_name 
 FROM traces 
 WHERE service_name = 'auth' 
 LIMIT 100
5. Dashboard Efficiency
Optimization Tips:
1. Limit panels per dashboard
2. Use appropriate refresh rates
3. Group related metrics
4. Archive unused dashboards

6. Maintenance Checklist
Daily Checks:

□ Monitor system health
□ Check error rates
□ Verify data ingestion
□ Review active alerts

Weekly Tasks:

□ Clean up old data
□ Review dashboard performance
□ Check resource usage
□ Update alert thresholds

Monthly Maintenance:

□ Audit user access
□ Review retention policies
□ Update documentation
□ Optimize queries

7. Scaling
Scale when indicators show;
1. High CPU usage (>70%)
2. Increased latency
3. Storage reaching 80%
4. Slow query responses

Actions to take in this scenerio include:
1. Increase resources
2. Adjust sampling
3. Optimize queries
4. Consider clustering

8. Security
1. Use Role-Based Access:
— Admin: Full access
— Developer: Write access
— Viewer: Read-only

2. API Security:
— Use secure tokens
— Rotate credentials
— Limit API access

9. Backup and Recovery
Regular Backups:
$ docker exec clickhouse-server 
 clickhouse-backup create
2. Verify Backups:

$ docker exec clickhouse-server 
 clickhouse-backup list
3. Recovery Process:

$ docker exec clickhouse-server 
 clickhouse-backup restore
Next Steps
After completing this setup:

Explore different metrics
Set up more detailed dashboards
Configure additional alerts
Add more applications
Fine-tune alert thresholds
Create custom dashboards for specific needs
Set up automated reports
Implement advanced tracing
