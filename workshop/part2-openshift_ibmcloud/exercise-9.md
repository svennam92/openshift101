# Exercise 9: Analyze your logs with LogDNA

## Exercise 9: Analyze your logs through the LogDNA web UI

In LogDNA, you can grant any of the following roles to users:

* `Mamager` role: This role grants permissions to a user to perform all admin tasks. As a manager, a user can manage resources: configure/manage/delete views, alerts, dashboards, screens, keys, and exclusion rules; export data; search, filter, and view all data; and configure archiving.
* `Standard member` role: This role grants permissions to a user to perform some admin tasks. As a standard member, a user can configure/manage/delete views, alerts, dashboards and screens, export data, and search, filter, and view all data.
* `Reader` role: This role grants permissions to a user to customize the UI, and view data only. As a reader, a user can perform read-only actions such as monitor data through views, dashboards, and screens.

Your user has been granted manager permnissions in the LogDNA instance that you are using in this lab so that you can complete all of the sections in the exercise.

**IMPORTANT: Use Chrome to complete this exercise.**

### Create a custom parser

**You can use the custom parser to extract additional information in custom fields that you can use to accelerate searches and enhance reporting in dashboards and screens.**

There are 3 status types for parsing templates;

* Active: Valid parsing templates that will be applied against log lines. Activated parsing templates are only applied to the lines that come in after the template has been enabled. All log lines that were ingested prior to the template becoming active are not parsed by the parsing template. 
* Inactive: Valid parsing templates that will not be applied against log lines.
* Draft: Invalid parsing templates. These are templates that are either incomplete, contain errors or have not yet been validated.

Comnplete the following steps to create a custom field for a type of log record:

Notice that this step requires that you have logged in to the sample app at least once.

1. In the LogDNA web UI, select the **Settings** icon ![Admin icon](../.gitbook/assets/admin.png). Then select **Parsing**.
2. Select **Create Template**. The _Choose Log Line_ wizard opens.

   ![parsing image 1](../.gitbook/assets/parsing-img-1.png)

3. Click **Select a method**. Choose **Find an existing log line**.

   ![parsing image 2](../.gitbook/assets/parsing-img-2.png)

4. Click **Query** to enter the following query: `namespace:demo AND app:patientui AND "endpoint for"`.

   ![parsing image 3](../.gitbook/assets/parsing-img-3.png)

   Then, click enter. A subset of log lines that match that query are listed.

   ![parsing image 4](../.gitbook/assets/parsing-img-4.png)

5. Select **Build Parsing Template**. The _Parsing Template_ wizard opens.
6. Choose the first criteria to parse the data.

   Click **Choose an extractor**. Then choose **Extract Values by Delimiter**.

   ![parsing image 5](../.gitbook/assets/parsing-img-5.png)

   Enter the following delimiter: **endpoint for**

   ![parsing image 6](../.gitbook/assets/parsing-img-6.png)

7. Choose the second criteria to parse the data.

   Select the username that you used to log in.

   ![parsing image 7](../.gitbook/assets/parsing-img-7.png)

   Then, click **choose an operator** and select **Trim value**.

   ![parsing image 8](../.gitbook/assets/parsing-img-8.png)

   The trim configuration options are displayed:

   ![parsing image 9](../.gitbook/assets/parsing-img-9.png)

   Enter `0` and `1` to remove any non-wanted characters.

   ![parsing image 10](../.gitbook/assets/parsing-img-10.png)

8. Configure the custom field.

   Click **Choose an extractor**. Then choose **Capture in Field**.

   ![parsing image 11](../.gitbook/assets/parsing-img-11.png)

   Enter the name of the custom field: **patient**

   ![parsing image 12](../.gitbook/assets/parsing-img-12.png)

9. Select **Proceed to validation**. The _Test and Verify_ wizard opens.

   ![parsing image 13](../.gitbook/assets/parsing-img-13.png)

   Check that all the lines set valid username values. Click **Validate All**.

   Lines should change the status to **Validated**.

   ![parsing image 14](../.gitbook/assets/parsing-img-14.png)

   Then, click **Activate**. A new template is created.

   ![parsing image 15](../.gitbook/assets/parsing-img-15.png)

After 15 min, new log records that arrive to the LogDNA instance and match the query criteria will include a custom field **patient** that you can use for filtering data in views, dashboards, and screens.

![parsing image 16](../.gitbook/assets/parsing-img-16.png)

### Create a custom view

**In LogDNA, you can configure custom views to monitor a subset of data. You can also attach an alert to a view to be notified of the presence or absence of log lines.**

When you launch the LogDNA web UI, log entries are displayed with a predefined format. You can modify in the **User Preferences** section how the information in each log line is displayed. You can also filter logs and modify search settings, then bookmark the result as a _view_. You can attach and detach one or more alerts to a view. You can define a custom format for how your lines are shown in the view. You can expand a log line and see the data parsed.

#### View events with the default format

1. In the LogDNA web UI, click the **Views** icon ![Configuration icon](../.gitbook/assets/views.png).
2. Select **Everything** to see all the events.

![Views image 1](../.gitbook/assets/views-img-1.png)

#### Customize your default view

In the **USER PREFERENCES** section, you can modify the order of the data fields that are displayed per line.

Complete the following steps to modify the format of a log line:

1. Select the **Configuration** icon ![Admin icon](../.gitbook/assets/admin.png).
2. Select **USER PREFERENCES**. A new window opens.
3. Select **Log Format**.
4. Modify the _Line Format_ section to match your requirements. Drag boxes around.

#### Create a custom view to monitor logs from the sample app

You can select the events that are displayed through a view by applying a search query in the search bar, selecting values in the search area, or a combination of both. You can save that view for reuse later.

Complete the following steps:

1. In the LogDNA web UI, select **ALL Apps** in the search area.

   ![Views image 2](../.gitbook/assets/views-img-2.png)

2. Select the app **patientui** which is the sample app that you have deployed in your cluster in the lab.

   ![Views image 3](../.gitbook/assets/views-img-3.png)

3. Filter out log lines to display only lines that tagged as debug lines. Enter in the search bar the following query: `level:debug`

   ![Views image 4](../.gitbook/assets/views-img-4.png)

   Then, click enter. The view will show lines that meet the filtering and search criterias.

   ![Views image 5](../.gitbook/assets/views-img-5.png)

4. Save the custom view.

   Click **Unsaved view**. Select **Save view**.

   ![Views image 6](../.gitbook/assets/views-img-6.png)

   The following pop up opens:

   ![Views image 7](../.gitbook/assets/views-img-7.png)

   Enter the name of the view. Use the following format: `<Enter your user name> patientUI` For example, `marisa patientui`

   ![Views image 8](../.gitbook/assets/views-img-8.png)

   Enter a category. Use the following format: `<Enter your user name>` For example, `marisa` Then click **Add new category**.

   ![Views image 9](../.gitbook/assets/views-img-9.png)

   Your configuration should look like the following sample:

   ![Views image 10](../.gitbook/assets/views-img-10.png)

   Click **Save view**.

A new category appears on the left navigation panel.

![Views image 11](../.gitbook/assets/views-img-11.png)

#### Analyze a log line

At any time, you can view each log line in context.

Complete the following steps:

1. Click the **Views** icon ![Configuration icon](../.gitbook/assets/views.png).
2. Select **Everything** or a view.
3. Identify a line in the log that you want to explore.
4. Expand the log line.

   Information about line identifiers, tags, and labels is displayed.

5. Click **View in Context** to see the log line in context of other log lines from that host, app, or both.

   ![Views image 12](../.gitbook/assets/views-img-12.png)

   A new pop up window opens.

   ![Views image 13](../.gitbook/assets/views-img-13.png)

   Choose one of the following options:

   **By Everything** to see the log line in the context of all log records \(everything\) that are available in the LogDNA instance.

   **By source** to see the log line in the context of the log lines for the same source.

   **By App** to see the log line in the context of the log lines of the app.

   **By Source and App** to see the log line in the combined context of the app and source.

6. Click **Copy to clipboard** to copy the message field to the clipboard.

When you are finished, close the line.

#### View a subset of the events by applying a timeframe

In a view, you can search events that are displayed through a view for a specific timeframe.

You can apply a timestamp by specifying an absolute time, a relative time, or a time range.

Complete the following steps to jump to a specific time: 1. Launch the LogDNA web UI. 2. Click the **Views** icon ![Configuration icon](../.gitbook/assets/views.png). 3. Select your custom view. 4. Enter a time query. Choose any of the following options:

* Enter an abosute time to jump to a point in time in your events such as `January 27 10:00am`.

  ![Views image 15](../.gitbook/assets/views-img-15.png)

* Enter a relative time such as `1 min ago`.

  ![Views image 16](fix_link)

  You can also enter a time range such as `yesterday 10am to yesterday 11am`, `last fri 4:30pm to 11/12 1 AM`, `last wed 4:30pm to 23/05 1 AM`, or `May 20 10am to May 22 10am`. Make sure to include `to` to separate the initial timestamp from the end timestamp.

1. Click **ENTER**.

You might get the error message: `Your request is taking longer than expected, try refreshing your browser in a bit as we try to catch up. Retry.` You might get this error when the timeframe that you have specified does not have any events available to show. Change the time query, and retry.

### Create a dashboard

**You can create a dashboard to monitor your app graphically through interactive graphs.**

For example, you can use graphs to analyze patterns and trends over a period of time.

Complete the following steps to create a dashboard to monitor logs from the lab's sample app:

1. In the LogDNA web UI, click the **Boards** icon ![Dashboards icon](../.gitbook/assets/boards.png).
2. Select **NEW BOARD** to create a new dashboard.

   ![Board image 1](../.gitbook/assets/board-img-1.png)

3. Click **Add graph**.

   ![Board image 2](../.gitbook/assets/board-img-2.png)

4. Select the field **app**.

   ![Board image 3](../.gitbook/assets/board-img-3.png)

   Then, select the value **patientui**.

   ![Board image 4](../.gitbook/assets/board-img-4.png)

   Click **Add graph**.

   ![Board image 5](../.gitbook/assets/board-img-5.png)

5. Open a view that displays the logs for the patientui app. Click the graph in a peak of data at the time that you want to see logs, and then click **Show logs**.

   ![Board image 6](../.gitbook/assets/board-img-6.png)

   A new page opens with the log entries.

   ![Board image 7](../.gitbook/assets/board-img-7.png)

6. Add subplots to analyze the data by applying additonal filtering criteria.

   ![Board image 8](../.gitbook/assets/board-img-8.png)

   Click **Show subplots**.

   ![Board image 9](../.gitbook/assets/board-img-9.png)

   Select **Histogram**.

   ![Board image 10](../.gitbook/assets/board-img-10.png)

   Select **level**.

   ![Board image 11](../.gitbook/assets/board-img-11.png)

   Click **Add** to configure more subplots. For example, add a histogram for the custom field _patient_ that you configured earlier. \(Note that you might need to wait a bit longer and logout and login into the sample app for the custom field to be available for search after the parsing template is applied. Continue and retry this step later on.\)

   ![Board image 12](../.gitbook/assets/board-img-12.png)

7. Name the dashboard.

   Enter `patientui` as the name of the dashboard.

   ![Board image 13](../.gitbook/assets/board-img-13.png)

   Enter a category. Use the following format: `<Enter your user name>` For example, `marisa` Then click **Add new category**.

   ![Board image 14](../.gitbook/assets/board-img-14.png)

   Your configuration should look like the following sample:

   ![Board image 15](../.gitbook/assets/board-img-15.png)

   Click **Save**.

A new category appears on the left navigation panel.

![Board image 16](../.gitbook/assets/board-img-16.png)

### Create a screen to monitor your app

**You can create a screen to monitor your app graphically through metrics \(counters\), operational KPIs \(gauges\), tables, and time-shifted graphs \(graphs that you can use to analyze patterns and trends for comparison analysis\).**

Complete the following steps to create a dashboard to monitor logs from the lab's sample app:

1. In the LogDNA web UI, click the **screens** icon ![Screens icon](../.gitbook/assets/screens.png).
2. Select **NEW SCREEN**.

   ![Screen image 1](../.gitbook/assets/screen-img-1.png)

3. Add a counter.

   Click **Add Widget**.

   ![Screen image 2](../.gitbook/assets/screen-img-2.png)

   Select **Count**.

   ![Screen image 3](../.gitbook/assets/screen-img-3.png)

   Click the widget. You will get the configuration fields for this widget.

   ![Screen image 4](../.gitbook/assets/screen-img-4.png)

   To configure the _Count_ widget to report on the log lines for the application patientui, you must select the field **app**, and set the value to **patientui**.

   ![Screen image 5](../.gitbook/assets/screen-img-5.png)

   You can also add a label, by entering a value for the _label_ field.

   ![Screen image 6](../.gitbook/assets/screen-img-6.png)

   The widget should look similar to the following one:

   ![Screen image 7](../.gitbook/assets/screen-img-7.png)

4. Add a gauge.

   Click **Add Widget**.

   ![Screen image 2](../.gitbook/assets/screen-img-2.png)

   Select **Gauge**.

   ![Screen image 8](../.gitbook/assets/screen-img-8.png)

   Click the widget. You will get the configuration fields for this widget.

   To configure the _Gauge_ widget to report on the debug log lines for the application patientui, you must select the field **level**, and set the value to **debug**. Then, set the advanced condition `app:patientui`. The duration is set to the default, last 1 day.

   ![Screen image 9](../.gitbook/assets/screen-img-9.png)

   Add a label, by entering a value for the _label_ field. Enter `PatientUI - INFO`. Add also the gauge limits.

   ![Screen image 10](../.gitbook/assets/screen-img-10.png)

   The widget should look similar to the following one:

   ![Screen image 11](../.gitbook/assets/screen-img-11.png)

5. Add a table.

   Click **Add Widget**.

   ![Screen image 2](../.gitbook/assets/screen-img-2.png)

   Select **Table**.

   ![Screen image 12](../.gitbook/assets/screen-img-12.png)

   Click the widget. You will get the configuration fields for this widget.

   To list the number of records in the last 24 hours for the cluster namespaces, you must set the field **namespace**.

   ![Screen image 13](../.gitbook/assets/screen-img-13.png)

   Change the default number of rows from 3 to 10.

   ![Screen image 14](../.gitbook/assets/screen-img-14.png)

   The widget should look similar to the following one:

   ![Screen image 15](../.gitbook/assets/screen-img-15.png)

6. Save the screen. Select **Save Screen**.

   IMPORTANT: If you do not save the screen, you loose all your widgets.

### Export resources for reuse in other LogDNA instances \(Optional\)

To avoid recreating definitions of views, boards, parsing templates, and exclusion rules, you can export these resources from a  instance as a JSON file. Then, you can import the definitions into other LogDNA instances.

Complete the following steps to export the configuration of your resources:

1. Launch the LogDNA web UI.
2. Select the **Settings** icon ![Admin icon](../.gitbook/assets/admin.png). Then select **Organization**.
3. Select **Account config**.
4. In the _Export configuration_ section, select the types of resources that you want to export.

   Notice that options are disabled if you do not have definitions of this type of resource in your LogDNA instance.

   You can export views and alerts, boards, parsing templates, and exclusion rules.

5. Select **Export configuration** and save the file.

The file that you save is a JSON file. You cannot modify it or you will break it.

Notice that when you import this file into another LogDNA instance, you can choose to add or replace the existing resources.

* When you choose the **add** option, you add assets to the exisiting ones.
* When you choose the **replace** option, you remove all assets, and new ones are created. Watch out with this option since you may delete resources that you might want to keep.

## Controlling data usage \(Optional\)

you can control the data that is collected and available for analysis through a LogDNA instance. You can define exclusion rules in the UI that apply to data collected in that instance. You can also configure LogDNA agents and customize them to collect and forward specific log data. In addition, you can define an alert that is triggered when the data usage threshold that you define for that LogDNA instance is reached.

#### Enable an email alert to notify of usage

You can define an alert to notify when the data usage in the instance reaches the data usage threshold that you set for the instance.

1. In the LogDNA web UI, select the **Settings** icon ![Admin icon](../.gitbook/assets/admin.png). Then select **Usage**.

   In the **Dashboard** section, you can see your data usage.

2. Define a **Usage Alert** to set the threshold for data usage in the instance. When the threshold is reached, you are notified. Enter a value to set the data usage threshold.
3. In the **Add recipient** section, enter one or more emails where the notification will be sent.

#### Configure an exclusion rule to stop showing logs from a resource

**A service administrator \(manager role\) can configure exclusion rules through the LogDNA web UI to stop logs from counting against your data usage quota and from being stored for search.**

In this section, you will learn how to exclude kube-system data from the cluster while keeping entries that report errors only.

You will configure the rule so that you are not able to see excluded log data through views.

Complete the following steps to define the exclusion rule:

1. In the LogDNA web UI, select the **Settings** icon ![Admin icon](../.gitbook/assets/admin.png). Then select **Usage** &gt; **Exclusion Rules**.
2. Select **Add Rule**. The **Create Rule** section opens.
3. Enter a name for the rule in the section **What is this rule for?**. For example, enter _Exclude log records from the namespace kube-system except error ones_.
4. Enter the exclusion criteria. You can select 1 or more sources, 1 or more apps, enter a query, or a combination of sources, apps and query.

   In the Query section, enter **Namespace:kube-system -level:error** to exclude all lines except the ones that report an error.

5. Leave unchecked the option **Preserve these lines for live-tail and alerting**.
6. Click **Save**.

