<properties
    pageTitle="疑難排解 Azure SQL Database 的「目前無法使用伺服器上的資料庫」"
    description="找出並解決 Azure SQL Database 連接錯誤的步驟。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="msmets"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="daleche"/>


# 疑難排解「目前無法使用伺服器上的資料庫。請稍後重試連接。」和其他連接錯誤

「 資料庫 <dbname> 伺服器上 <servername> 不存在...」 的 Azure SQL Database 是最常見的暫時性連接錯誤。暫時性連接錯誤通常是因為平台重新設定而造成，例如，容錯移轉至新的伺服器或暫存系統故障，而且時間很短。如果您要取得不同的錯誤，評估 [錯誤訊息](sql-database-develop-error-messages.md) 原因的線索，判斷問題是否為暫時性或永久性，以及使用本主題中的指南。

## 解決暫時性連線問題的步驟

1.  請確定您的應用程式使用重試邏輯。 請參閱 [連線問題](sql-database-connectivity-issues.md) 和 [設計指導方針和最佳作法](sql-database-connect-central-recommendations.md) 一般重試策略。 然後請參閱 [程式碼範例](sql-database-develop-quick-start-client-code-samples.md) 如需詳細資訊。
2.  由於資料庫接近其資源限制，因此看起來可能像是暫時性連線問題。 請參閱 [移難排解效能問題](sql-database-troubleshoot-performance.md)。
3.  如果連線持續發生問題，請連絡支援人員開啟支援案例。

## 解決永久性連線問題的步驟

如果應用程式完全無法連線，通常是 IP 和防火牆設定的問題。 這可能包括用戶端上的網路重新設定 (例如，新的 IP 位址或 Proxy)。 連接參數輸入錯誤 (例如連接字串) 也很常見。

1.  設定 [防火牆規則](sql-database-configure-firewall-settings.md) 允許用戶端 IP 位址。
2.  在用戶端與網際網路之間的所有防火牆上，請確定開放連接埠 1433 供輸出連線使用。
3.  請確認您的連接字串和其他連線設定。 請參閱中的連接字串 [連線問題主題](sql-database-connectivity-issues.md)。
4.  檢查儀表板中的服務健全狀況。 如果您認為沒有區域性停電時，請參閱 [從中斷復原](sql-database-disaster-recovery.md) 的步驟來復原到新的區域。
5.  如果連線持續發生問題，請連絡支援人員開啟支援案例。





