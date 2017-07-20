---
title: "A helyszíni identitás-infrastruktúra megfigyelése a felhőben."
description: "Leírását és esetleges használatának okát lásd az Azure AD Connect Health vonatkozó lapján."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 881ce13b6e4b10064294e590431434b29da3fb33
ms.contentlocale: hu-hu
ms.lasthandoff: 07/19/2017

---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben
Az Azure Active Directory (Azure AD) Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe. Megbízható Office 365- és Microsoft Online Services-kapcsolat fenntartását teszi lehetővé olyan fontos identitás-összetevők megfigyelésével, mint például az Active Directory összevonási szolgáltatások (AD FS) kiszolgálói, az Azure AD Connect-kiszolgálók (más néven a szinkronizálási motor) vagy az Active Directory-tartományvezérlők stb. Ezen összetevők legfontosabb adatpontjait is könnyen hozzáférhetővé teszi, így lekérdezhetővé válnak a használatra és egyéb lényeges jellemzőkre vonatkozó adatok, hogy megalapozott döntéseket hozhasson.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat, használatelemzési információkat és egyéb információkat tekinthet meg. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Az Azure AD Connect Health funkcióinak bővülésével a portál egy identitásalapú irányítópultot kínál. Ezáltal egy nagy teljesítményű, kifogástalan állapotú és integrált környezetet biztosít, ahol a felhasználók még hatékonyabban dolgozhatnak.

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások eléréséhez közös identitás áll a rendelkezésükre. Ez az integráció azonban szükségessé teszi a környezet megfelelő állapotának biztosítását, hogy a felhasználók bármilyen eszközről megbízhatóan hozzáférhessenek a helyszíni és a felhőalapú erőforrásokhoz is. Az Azure AD Connect Health segít megfigyelni az Office 365 és más Azure AD-alkalmazások eléréséhez használt helyszíni identitás-infrastruktúrát, és további betekintést nyújt annak működésébe. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health for AD FS](active-directory-aadconnect-health-adfs.md)
Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012 és a Windows Server 2012 R2 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerű és alacsony költséggel végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS alábbi képességei:

* Riasztásokkal kiegészített megfigyelés az AD FS és az AD FS-proxykiszolgálók nem megfelelő állapotának észleléséhez;
* kritikus riasztásokhoz kapcsolódó e-mail értesítések;
* Teljesítményadat-tendenciák, amelyek hasznosak az AD FS kapacitástervezéséhez;
* Az AD FS használatának különböző területekre (például appokra, felhasználókra vagy hálózati helyekre) kiterjedő elemzései, amelyekkel felmérhetők az AD FS használatának módjai;
* AD FS-jelentések, például a rossz felhasználónév/jelszó párossal legtöbbet próbálkozó 50 felhasználó, az utolsó IP-címükkel együtt

Az alábbi videó az Azure AD Connect Health for AD FS működéséről nyújt áttekintést.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health szinkronizálási szolgáltatás](active-directory-aadconnect-health-sync.md)
Az Azure AD Connect Health szinkronizálási szolgáltatás figyelemmel kíséri a helyszíni Active Directory és az Azure AD közötti szinkronizálásokat, és információkat biztosít róluk. Az Azure AD Connect Health for Sync legfontosabb képességei a következők:

* Riasztásokkal kiegészített monitorozás az Azure AD Connect-kiszolgáló (más néven a szinkronizálási motor) nem megfelelő állapotának észleléséhez;
* kritikus riasztásokhoz kapcsolódó e-mail értesítések;
* A szinkronizálási műveletek elemzései, beleértve a rájuk vonatkozó késési diagramokat és olyan különböző műveletek tendenciáit, mint például a hozzáadások, a frissítések vagy a törlések;
* A szinkronizálási tulajdonságok és az Azure AD-re történt legutóbbi sikeres exportálási művelet gyors áttekintése
* Az objektumszintű szinkronizációs hibákról való jelentésekhez \(nem szükséges Prémium szintű Microsoft Azure AD\)

Az alábbi videó az Azure AD Connect Health szinkronizálás szolgáltatás működéséről nyújt áttekintést.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health for AD DS](active-directory-aadconnect-health-adds.md)
Az Active Directory Domain Serviceshez (AD DS) készült Azure AD Connect Health a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 vagy Windows Server 2016 rendszeren telepített tartományvezérlők figyelését teszi lehetővé. Az állapotügynök telepítése lehetővé teszi, hogy a helyszíni AD DS-környezetet a felhőből figyelje meg. Az Azure AD Connect Health for AD DS legfontosabb képességei a következők:

* Riasztások figyelése a tartományvezérlő megfelelő állapotának észlelése érdekében, valamint e-mailes értesítések küldése a kritikus riasztásokról
* A tartományvezérlő irányítópultja gyors betekintést biztosít a tartományvezérlők állapotába és üzemeltetési állapotába
* A Replikáció állapota irányítópult, amely a legújabb replikációs adatokkal rendelkezik, a hibák észlelésekor hibaelhárítási útmutatókra mutató hivatkozásokkal szolgál
* Gyors és helyfüggetlen hozzáférést biztosít a népszerű teljesítményszámlálók teljesítményadat-grafikonjaihoz, melyek hibaelhárítási és figyelési célból szükségesek

Az alábbi videó az Azure AD Connect Health for AD DS működéséről nyújt áttekintést.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Az Azure AD Connect Health használatának első lépései
Az Azure AD Connect Health használatának elkezdéséhez hajtsa végre a következő lépéseket:

1. [Szerezze be a Prémium szintű Azure AD-t](../active-directory-get-started-premium.md) vagy [indítson egy próbaverziót](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Töltse le és telepítse az Azure AD Connect Health-ügynököket](#download-and-install-azure-ad-connect-health-agent) az identitás-kiszolgálókra.
3. Az Azure AD Connect Health irányítópultját a következő címen tekintheti meg: [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)

> [!NOTE]
> Ne feledje, hogy mielőtt bármilyen adatot megtekinthetne az Azure AD Connect Health irányítópultján, telepítenie kell az Azure AD Connect Health-ügynököket a célkiszolgálókon.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Az Azure AD Connect Health-ügynök letöltése és telepítése
* Győződjön meg róla, hogy [teljesülnek az Azure AD Connect Health követelményei](active-directory-aadconnect-health-agent-install.md#requirements).
* Ismerkedés az Azure AD Connect Health for AD FS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD FS szolgáltatáshoz.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Tekintse meg a telepítési utasításokat](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Ismerkedés az Azure AD Connect Health szinkronizálási szolgáltatás használatával
    * [Töltse le, és telepítse az Azure AD Connect legújabb verzióját.](http://go.microsoft.com/fwlink/?linkid=615771) A szinkronizálási állapotügynök az (1.0.9125.0-s verziójú vagy újabb) Azure AD Connect részeként telepíthető.
* Ismerkedés az Azure AD Connect Health for AD DS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD DS szolgáltatáshoz](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Tekintse meg a telepítési utasításokat](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Az Azure AD Connect Health portál
Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat és használatelemzési információkat tekinthet meg. Az Azure AD Connect Health fő panelje a https://aka.ms/aadconnecthealth címen található. A panelek az ablakoknak megfelelő funkciót töltenek be. A fő panelen a **Gyors üzembe helyezés**, az Azure AD Connect Health szolgáltatásai és további konfigurációs lehetőségek láthatók. Tekintse meg az alábbi képernyőfelvételt, valamint az azt követő rövid magyarázatot. Az ügynökök üzembe helyezése után az állapotfigyelő szolgáltatás automatikusan azonosítja az Azure AD Connect Health által monitorozott szolgáltatásokat.

> [!NOTE]
> A licencelési információkért lásd: [Azure AD Connect – gyakori kérdések](active-directory-aadconnect-health-faq.md) vagy [Az Azure AD díjszabási oldala](https://aka.ms/aadpricing).
    
![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/portal4.png)

* **Quick Start** (Gyors üzembe helyezés): Ha ezt a beállítást választja, megnyílik a **gyors üzembe helyezés** panelje. A **Get Tools** (Eszközök beszerzése) lehetőség kiválasztásával letöltheti az Azure AD Connect Health-ügynököt. Emellett hozzáférhet a dokumentációkhoz, és visszajelzést is küldhet.
* **Active Directory Federation Services** (Active Directory összevonási szolgáltatások): Ez a lehetőség az Azure AD Connect Health által aktuálisan monitorozott összes AD FS-szolgáltatást mutatja. Ha kiválaszt egy példányt, a megnyíló panel az adott szolgáltatáspéldányra vonatkozó információkat tartalmazza. Az információk között tulajdonságokat, riasztásokat, megfigyelési adatokat, használatelemzést és egy áttekintést talál. További információ ezekről a képességekről: [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (Sync)** ((Szinkronizálási) Azure Active Directory Connect): Ez a lehetőség az Azure AD Connect Health által aktuálisan monitorozott Azure AD Connect-kiszolgálókat mutatja. Ha kiválaszt egy bejegyzést, a megnyíló panel az Azure AD Connect-kiszolgálókra vonatkozó információkat tartalmazza. További információ ezekről a képességekről: [Az Azure AD Connect Health szinkronizálási szolgáltatás használata az AD FS szolgáltatással](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services** (Active Directory tartományi szolgáltatások): Ez a lehetőség az Azure AD Connect Health által aktuálisan figyelt összes AD DS-erdőt mutatja. Ha kiválaszt egy erdőt, a megnyíló panel az adott erdőre vonatkozó információkat tartalmazza. Ezek az adatok a következők áttekintését teszik lehetővé: lényeges információk, tartományvezérlő irányítópult, replikáció állapota irányítópult, riasztások és figyelés. További információ ezekről a képességekről: [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md).
* **Configure** (Konfigurálás): Ez a szakasz az alábbi funkciók be- vagy kikapcsolására szolgáló lehetőségeket tartalmaz:

  - Az automatikus frissítés engedélyezésével az Azure AD Connect Health-ügynöknek mindig a legfrissebb verziója érhető el. Az Azure AD Connect Health-ügynök automatikusan frissül a legfrissebb verzióra, amint az megjelenik. Ez e beállítás alapértelmezés szerint engedélyezve van.
  - Engedélyezheti a Microsoft számára az Azure AD-címtár állapotadataihoz való hozzáférést hibaelhárítási céllal. Ha a beállítás engedélyezve van, a Microsoft ugyanazokat az adatokat láthatja, amelyeket Ön. Ez az információ a hibaelhárítás és a problémakezelés során jelenthet segítséget. A beállítás alapértelmezés szerint le van tiltva.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](active-directory-aadconnect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)

