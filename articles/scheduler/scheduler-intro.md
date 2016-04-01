<properties
 pageTitle="何謂 Azure 排程器？ | Microsoft Azure"
 description="Azure 排程器可讓您以宣告方式描述在雲端中執行的動作。 然後它會排程這些動作並且自動執行。"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# 何謂 Azure 排程器？

Azure 排程器可讓您以宣告方式描述在雲端中執行的動作。 然後它會排程這些動作並且自動執行。  排程器會使用 [Azure 入口網站](scheduler-get-started-portal.md), ，程式碼中， [REST API](https://msdn.microsoft.com/library/dn528946), ，或 Azure PowerShell。

排程器會建立、 維護和叫用已排程的工作。  排程器不會裝載任何工作負載或執行任何程式碼。 它只 _會叫用_ 其他位置裝載程式碼 — 在 Azure 中，為內部部署或另一個提供者。 它會透過 HTTP、HTTPS 或儲存體佇列叫用。

排程器排程 [工作](scheduler-concepts-terms.md), ，會保留工作執行結果的記錄，其中一個可以檢閱，並以決定性的方式且可靠地排程要執行的工作負載。 Azure WebJobs (Azure App Service 之 Web Apps 功能的一部分) 和其他 Azure 排程功能會在背景使用排程器。  [排程器 REST API](https://msdn.microsoft.com/library/dn528946) 協助管理這些動作的通訊。 因此，排程器支援 [複雜的排程和進階的週期](scheduler-advanced-complexity.md) 輕鬆。

需要使用排程器的案例有幾個。 例如：

+ _週期性應用程式動作 ︰_ 定期蒐集 Twitter 摘要。
+ _每日維護 ︰_ 每日剪除記錄、 執行備份及其他維護工作。 例如，系統管理員可以選擇備份的資料庫在凌晨 1:00 接下來的 9 個月的每一天。

排程器可讓您建立、 更新、 刪除、 檢視和管理工作和 [工作集合](scheduler-concepts-terms.md) 以程式設計的方式，利用指令碼，並在入口網站。

## 另請參閱

 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)


