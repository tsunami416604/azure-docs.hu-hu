---
title: "Mit tartalmaznak az Azure RemoteApp sablon rendszerképei? | Microsoft Docs"
description: "Az Azure RemoteApphez mellékelt sablon rendszerképek ismertetése."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7f8442b2-81da-421e-a453-aa53ba2066b7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 348306795fd3c8275b21e4ec6dceae408916bf72
ms.lasthandoff: 03/31/2017


---
# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Mit tartalmaznak az Azure RemoteApp sablon rendszerképei?
> [!IMPORTANT]
> Az Azure RemoteApp 2017. augusztus 31-ét követően megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Az Azure RemoteApp-előfizetés három sablon rendszerképet tartalmaz:

* Windows Server 2012
* Microsoft Office 365 ProPlus (a használatához Office 365-előfizetés szükséges)
* Microsoft Office 2013 Professional Plus (csak próbaverzió)

> [!IMPORTANT]
> Az Azure RemoteApp előfizetés hozzáférést biztosít a rendszerképeken lévő szoftverekhez, a különálló előfizetést igénylő Office 365 ProPlus, , valamint a termelési környezetben nem használható Office 2013 kivételével. Ez azt jelenti, hogy megoszthatja a rendszerképeken lévő programokat és alkalmazásokat a felhasználóival. Ha például létrehoz egy gyűjteményt, amely a Windows Server 2012 R2 rendszerképet használja, közzéteheti a System Center Endpoint Protectiont a felhasználók számára, akik a RemoteAppot használhatják az elérésére.
> 
> Az Office licenckezelési információiért tekintse meg a [RemoteApp licensing details](remoteapp-licensing.md) (A RemoteApp licenckezelési részletei), valamint a [Using Office with Azure RemoteApp](remoteapp-o365.md) (Az Office és az Azure RemoteApp használata) című témakört.
> 
> 

Az egyes rendszerképek tartalmának részleteiért olvasson tovább.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 („az eredeti rendszerkép”)
Ez a rendszerkép a Microsoft Windows Server 2012 R2 Datacenter operációs rendszeren alapul, és a következő szerepkörök és szolgáltatások vannak rajta telepítve, hogy megfeleljen az Azure RemoteApp sablon rendszerképek követelményeinek:

* .NET-keretrendszer 4.5, 3.5.1, 3.5
* Asztali élmény
* Szabadkézi beviteli és kézírás-felismerő szolgáltatások
* Multimédia alaprendszer
* Távoli asztali munkamenetgazda
* Windows PowerShell 4.0
* Windows PowerShell integrált parancsprogram-kezelési környezet (ISE)
* WoW64-támogatás

A rendszerképen továbbá a következő alkalmazások vannak telepítve:

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Microsoft Windows Media Player

## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (a használatához előfizetés szükséges)
Az Office 365 a legtöbbek által kért alkalmazás, ezért létrehoztunk egy „egyéni” rendszerképet, amellyel dolgozhat.

Ez a rendszerkép az eredeti rendszerkép bővített verziója, és a Microsoft Office 365 ProPlus következő összetevői vannak rajta telepítve a Windows Server 2012 R2 rendszerképnél ismertetett összetevőkön felül:

* Access
* Excel
* Lync
* OneNote
* OneDrive Vállalati verzió (megjegyzés: az Azure RemoteApp nem támogatja a szinkronizálási ügynök használatát)
* Outlook
* PowerPoint
* Word
* Microsoft Office Nyelvi ellenőrző eszközök

A rendszerkép tartalmazza a Visio Pro és a Project Pro alkalmazásokat is.

Emellett a következő alkalmazások is szerepelnek benne:

* SQL Native Client
* ODBC-illesztő
* SQL Server Data Mining-ügyfél
* MasterDataServices-ügyfél
* Microsoft Publisher
* PowerQuery
* PowerMap

Az Office 365 ProPlus-alkalmazások összes funkciója csak az Office 365 ProPlus-előfizetéssel rendelkező felhasználók számára érhető el. Az Office 365 előfizetési csomagokkal kapcsolatos további információkért lásd az [Office 365 service plans](http://technet.microsoft.com/library/office-365-plan-options.aspx) (Office 365-szolgáltatásicsomagok) című témakört. További kérdései vannak? Tekintse meg az [Office 365 + RemoteApp](remoteapp-o365.md) információit. Emellett olvassa el a [How to use your Office 365 subscription with Azure RemoteApp](remoteapp-officesubscription.md) (Az Office 365-előfizetés és az Azure RemoteApp használata) című új cikket.

Vegye figyelembe, hogy az Office 365 ProPlus, a Visio Pro és a Project Pro külön licencelést igényel – ezek mindegyike saját licenccel rendelkezik.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (csak próbaverzió)
Az ingyenes próbaidőszak során a szolgáltatást az Office 2013-rendszerképpel tesztelheti.

Ez a rendszerkép az eredeti rendszerkép bővített verziója, és a Microsoft Office 2013 Professional Plus következő összetevői vannak rajta telepítve a Windows Server 2012 R2 rendszerképnél ismertetett összetevőkön felül:

* Access
* Excel
* Lync
* OneNote
* OneDrive Vállalati verzió (megjegyzés: az Azure RemoteApp nem támogatja a szinkronizálási ügynök használatát)
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Microsoft Office Nyelvi ellenőrző eszközök

> [!IMPORTANT]
> **Jogi információk:** Ez a rendszerkép nem tartalmaz Microsoft Office-licencet, és *nem használható termelési környezetben*. Az Office 2013 Professional Plus-rendszerkép csak próbaverziós felhasználásra szolgál. Ha Office-alkalmazásokat kíván használni az Azure RemoteAppban termelési környezetben, az Office 365 ProPlus-rendszerképet kell használnia. Az Office licenckezelésével kapcsolatos további részletekért tekintse meg a [Using Office 365 with Azure RemoteApp](remoteapp-o365.md) (Az Office és az Azure RemoteApp használata) című témakört
> 
> 


