<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg"></tags>

# Get started with SQL database auditing

Azure SQL Database Auditing tracks database events and writes audited events to an audit log in your Azure Storage account. Auditing is available in preview for Basic, Standard, and Premium service tiers. To use auditing, [sign up for the preview][sign up for the preview].

Auditing can help you maintain regulatory compliance, understand database activity, and gain insight into discrepancies and anomalies that could indicate business concerns or suspected security violations.

Auditing tools enable and facilitate adherence to compliance standards but don't guarantee compliance. For more information about Azure programs that support standards compliance, see the [Azure Trust Center][Azure Trust Center].

-   [Azure SQL Database Auditing basics][Azure SQL Database Auditing basics]
-   [Set up auditing for your database][Set up auditing for your database]
-   [Analyze audit logs and reports][Analyze audit logs and reports]

## <span id="subheading-1"></span>Azure SQL Database Auditing basics</a>

You set up auditing in the Azure Preview Portal, but it makes no difference whether you created the database using the Azure Portal or the Azure Preview Portal. SQL Database auditing enables you to:

-   **Retain** an audit trail of selected events. Define categories of database actions and events to be logged.
-   **Report** on database activity. Use preconfigured reports and a dashboard to get started quickly with activity and event reporting.
-   **Analyze** reports. Find suspicious events, unusual activity, and trends.

You can audit the following activities and events:

-   **Access to data**
-   **Schema changes (DDL)**
-   **Data changes (DML)**
-   **Accounts, roles, and permissions (DCL)**
-   **Security exceptions**

For further detail about the the activities and events logged, see the [Audit Log Format Reference (doc file download)][Audit Log Format Reference (doc file download)].

You also choose the storage account where audit logs will be saved.

### Security-enabled connection string

When you set up auditing, Azure provides you with a security-enabled connection string for the database. Only client applications that use this connection string will have their activity and events logged, so you need to update existing client applications to use the new string format.

Traditional connection string format: \<*server name*\>.database.windows.net

Security-enabled connection string: \<*server name*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Set up auditing for your database

1.  [Sign up for the Auditing preview][sign up for the preview].
2.  Launch the [Azure Preview Portal][Azure Preview Portal] at <https://portal.azure.com>.
3.  Click the database you want to audit, and then click **Auditing Preview** to enable the auditing preview and launch the auditing configuration blade.

    ![][0]

4.  In the auditing configuration blade, select the Azure storage account where logs will be saved. **Tip:** Use the same storage account for all audited databases to get the most out of the preconfigured reports templates.

    ![][1]

5.  Under **Auditing Options**, click **All** to log all events, or choose individual event types.

    ![][2]

6.  Check **Save this Configuration as Default** if you want these settings to apply to all future databases on the server, and any that don't already have auditing set up. You can override the settings later for each database by following these same steps.

    ![][3]

7.  Click **Show database connection strings** and then copy or make a note of the appropriate security enabled connection string for your application. Use this string for any client applications whose activity you want to audit.

    ![][4]

8.  Click **OK**.

## <span id="subheading-3"></span>Analyze audit logs and reports</a>

Audit logs are aggregated in a single Azure Store Table named **AuditLogs** in the Azure storage account you chose during setup. You can view log files using a tool such as [Azure Storage Explorer][Azure Storage Explorer].

A preconfigured dashboard report template is available as a [downloadable Excel spreadsheet][downloadable Excel spreadsheet] to help you quickly analyze log data. To use the template on your audit logs, you need Excel 2013 or later and Power Query, which you can download [here][here].

The template has fictional sample data in it, and you can set up Power Query to import your audit log directly from your Azure storage account.

For more detailed instructions on working with the report template, read the [How To (doc download)][How To (doc download)].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [sign up for the preview]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure Trust Center]: http://azure.microsoft.com/en-us/support/trust-center/compliance/
  [Azure SQL Database Auditing basics]: #subheading-1
  [Set up auditing for your database]: #subheading-2
  [Analyze audit logs and reports]: #subheading-3
  [Audit Log Format Reference (doc file download)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [Azure Preview Portal]: https://portal.azure.com
  [0]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [downloadable Excel spreadsheet]: http://go.microsoft.com/fwlink/?LinkId=403540
  [here]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [How To (doc download)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
