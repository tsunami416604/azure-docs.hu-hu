<properties
    pageTitle="Mit tartalmaznak az Azure RemoteApp sablon rendszerképei? | Microsoft Azure"
    description="Az Azure RemoteApphez mellékelt sablon rendszerképek ismertetése."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/05/2016"
    ms.author="elizapo" />

# Mit tartalmaznak az Azure RemoteApp sablon rendszerképei?

Az Azure RemoteApp-előfizetés három sablon rendszerképet tartalmaz:


- Windows Server 2012
- Microsoft Office 365 ProPlus (a használatához Office 365-előfizetés szükséges)
- Microsoft Office 2013 Professional Plus (csak próbaverzió)

> [AZURE.IMPORTANT]Az Azure RemoteApp előfizetés hozzáférést biztosít a rendszerképeken lévő szoftverekhez, a különálló előfizetést igénylő Office 365 ProPlus, , valamint a termelési környezetben nem használható Office 2013 kivételével. Ez azt jelenti, hogy megoszthatja a rendszerképeken lévő programokat és alkalmazásokat a felhasználóival. Ha például létrehoz egy gyűjteményt, amely a Windows Server 2012 R2 rendszerképet használja, közzéteheti a System Center Endpoint Protectiont a felhasználók számára, akik a RemoteAppot használhatják az elérésére.
>
> Az Office licenckezelési információiért tekintse meg a [RemoteApp licensing details](remoteapp-licensing.md) (A RemoteApp licenckezelési részletei), valamint a [Using Office with Azure RemoteApp](remoteapp-o365.md) (Az Office és az Azure RemoteApp használata) című témakört.

Az egyes rendszerképek tartalmának részleteiért olvasson tovább.

## Windows Server 2012 R2 („az eredeti rendszerkép”)
Ez a rendszerkép a Microsoft Windows Server 2012 R2 Datacenter operációs rendszeren alapul, és a következő szerepkörök és szolgáltatások vannak rajta telepítve, hogy megfeleljen az Azure RemoteApp sablon rendszerképek követelményeinek:


- .NET-keretrendszer 4.5, 3.5.1, 3.5
- Asztali élmény
- Szabadkézi beviteli és kézírás-felismerő szolgáltatások
- Multimédia alaprendszer
- Távoli asztali munkamenetgazda
- Windows PowerShell 4.0
- Windows PowerShell integrált parancsprogram-kezelési környezet (ISE)
- WoW64-támogatás

A rendszerképen továbbá a következő alkalmazások vannak telepítve:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (a használatához előfizetés szükséges)
Az Office 365 a legtöbbek által kért alkalmazás, ezért létrehoztunk egy „egyéni” rendszerképet, amellyel dolgozhat.

Ez a rendszerkép az eredeti rendszerkép bővített verziója, és a Microsoft Office 365 ProPlus következő összetevői vannak rajta telepítve a Windows Server 2012 R2 rendszerképnél ismertetett összetevőkön felül:


- Access
- Excel
- Lync
- OneNote
- OneDrive Vállalati verzió
- Outlook
- PowerPoint
- Word
- Microsoft Office Nyelvi ellenőrző eszközök

A rendszerkép tartalmazza a Visio Pro és a Project Pro alkalmazásokat is.

Emellett a következő alkalmazások is szerepelnek benne:

- SQL Native Client
- ODBC-illesztő
- SQL Server Data Mining-ügyfél
- MasterDataServices-ügyfél
- Microsoft Publisher
- PowerQuery
- PowerMap


Az Office 365 ProPlus-alkalmazások összes funkciója csak az Office 365 ProPlus-előfizetéssel rendelkező felhasználók számára érhető el. Az Office 365 előfizetési csomagokkal kapcsolatos további információkért lásd az [Office 365 service plans](http://technet.microsoft.com/library/office-365-plan-options.aspx) (Office 365-szolgáltatásicsomagok) című témakört. További kérdései vannak? Tekintse meg az [Office 365 + RemoteApp](remoteapp-o365.md) információit. Emellett olvassa el a [How to use your Office 365 subscription with Azure RemoteApp](remoteapp-officesubscription.md) (Az Office 365-előfizetés és az Azure RemoteApp használata) című új cikket.

Vegye figyelembe, hogy az Office 365 ProPlus, a Visio Pro és a Project Pro külön licencelést igényel – ezek mindegyike saját licenccel rendelkezik.

## Microsoft Office 2013 Professional Plus (csak próbaverzió)
Az ingyenes próbaidőszak során a szolgáltatást az Office 2013-rendszerképpel tesztelheti.

Ez a rendszerkép az eredeti rendszerkép bővített verziója, és a Microsoft Office 2013 Professional Plus következő összetevői vannak rajta telepítve a Windows Server 2012 R2 rendszerképnél ismertetett összetevőkön felül:


- Access
- Excel
- Lync
- OneNote
- OneDrive Vállalati verzió
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office Nyelvi ellenőrző eszközök

> [AZURE.IMPORTANT]**Jogi információk:** Ez a rendszerkép nem tartalmaz Microsoft Office-licencet, és *nem használható termelési környezetben*. Az Office 2013 Professional Plus-rendszerkép csak próbaverziós felhasználásra szolgál. Ha Office-alkalmazásokat kíván használni az Azure RemoteAppban termelési környezetben, az Office 365 ProPlus-rendszerképet kell használnia. Az Office licenckezelésével kapcsolatos további részletekért tekintse meg a [Using Office 365 with Azure RemoteApp](remoteapp-o365.md) (Az Office és az Azure RemoteApp használata) című témakört



<!--HONumber=Jun16_HO2-->


