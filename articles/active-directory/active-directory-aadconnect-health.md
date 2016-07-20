<properties
    pageTitle="A helyszíni identitás-infrastruktúra megfigyelése a felhőben."
    description="Leírását és esetleges használatának okát lásd az Azure AD Connect Health vonatkozó lapján."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/21/2016"
    ms.author="vakarand"/>

# A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben

Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  Megbízható Office 365- és Microsoft Online Services-kapcsolat fenntartását teszi lehetővé olyan fontos identitás-összetevők megfigyelésével, mint például az AD FS-kiszolgálók, az Azure AD Connect-kiszolgálók (más néven a szinkronizálási motor) vagy az Active Directory-tartományvezérlők. Ezen összetevők legfontosabb adatpontjait is könnyen hozzáférhetővé teszi, így könnyen lekérdezhetővé válnak a használatra és egyéb lényeges jellemzőkre vonatkozó adatok.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat, használatelemzési információkat és sok más tartalmat tekinthet meg. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Az Azure AD Connect Health jövőbeni frissítései további figyelési szolgáltatásokat és további identitás-összetevőkkel kapcsolatos elemzési lehetőségeket biztosítanak majd. A szolgáltatás identitásalapú irányítópultja egy nagyteljesítményű, kifogástalan állapotú és integrált környezetet biztosít, ahol a felhasználók még hatékonyabb munkavégzésre lesznek képesek.

<!-- <center>![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)</center> -->

## Miért érdemes az Azure AD Connect Health eszközt használni?

A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Ez az integráció azonban komoly kihívásokat támaszt a környezet megfelelő állapotával szemben, hiszen a felhasználók számára kizárólag ilyen körülmények között garantálható mind a helyszíni, mind a felhőerőforrások megbízható hozzáférése, bármilyen eszközről. Az Azure AD Connect Health egyszerű felhőalapú megközelítése révén segít megfigyelni az Office 365 és más Azure AD-alkalmazások hozzáféréséhez használt helyszíni identitás-infrastruktúrát, és további betekintést nyújt annak működésébe. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

## [Azure AD Connect Health for AD FS](active-directory-aadconnect-health-adfs.md)

Az Azure AD Connect Health for AD FS a következő verziókat támogatja: AD FS 2.0 Windows Server 2008 R2-ben, AD FS Windows Server 2012-ben és Windows Server 2012 R2-ben. Ebbe beleértendők az AD FS proxy- vagy webalkalmazás-proxykiszolgálók is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök nagyon egyszerűen és alacsony költséggel végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS képességei, amelyek közül a következők emelendők ki:

- riasztásokkal kiegészített megfigyelés az AD FS és az AD FS-proxykiszolgálók nem megfelelő állapotának észleléséhez;
- kritikus riasztásokhoz kapcsolódó e-mail értesítések;
- teljesítményadat-tendenciák megtekintése, amely hasznosan alkalmazható az AD FS kapacitástervezéséhez;
- az AD FS használatának különböző területekre (például alkalmazásokra, felhasználókra vagy hálózati helyekre) kiterjedő elemzései, amelyekkel felmérhetők az AD FS használatának módjai;
- AD FS-jelentések, például a rossz felhasználónév/jelszó párossal legtöbbet próbálkozó 50 felhasználó.

Az alábbi videó az Azure AD Connect Health for AD FS működéséről nyújt áttekintést.

>[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]

## [Azure AD Connect Health szinkronizálási szolgáltatás](active-directory-aadconnect-health-sync.md)
Az Azure AD Connect Health for Sync figyelemmel kíséri a helyszíni Active Directory és az Azure Active Directory közötti szinkronizálásokat, és rájuk vonatkozó információkat biztosít. Az Azure AD Connect Health for Sync legfontosabb képességei a következők:

- riasztásokkal kiegészített megfigyelés az Azure AD Connect-kiszolgálók (más néven a szinkronizálási motor) nem megfelelő állapotának észleléséhez;
- kritikus riasztásokhoz kapcsolódó e-mail értesítések;
- a szinkronizálási műveletek elemzései, beleértve a rájuk vonatkozó késési diagramokat és olyan szinkronizálási műveletek tendenciáit, mint például a hozzáadások, a frissítések vagy a törlések;
- a szinkronizálási tulajdonságok és az Azure AD-re történt legutóbbi sikeres exportálási művelet gyors áttekintése.

Az alábbi videó az Azure AD Connect Health for Sync működéséről nyújt áttekintést.

[Azure Active Directory Connect Health: a szinkronizálási motor megfigyelése](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine)


## Az Azure AD Connect Health használatának első lépései
Az Azure AD Connect Health használatának megkezdése nagyon egyszerű. Kövesse az alábbi lépéseket:

1. [Szerezze be a Prémium szintű Azure AD-t](active-directory-get-started-premium) vagy [indítson egy próbaverziót](https://azure.microsoft.com/trial/get-started-active-directory/).

2. [Töltse le és telepítse az Azure AD Connect Health-ügynököket](#download-and-install-azure-ad-connect-health-agent) az identitás-kiszolgálókra.

3. Az Azure AD Connect Health irányítópultját a következő címen tekintheti meg: [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)

>[AZURE.NOTE]Ne feledje, hogy mielőtt bármilyen adatot megtekinthetne az Azure AD Connect Health irányítópultján, telepítenie kell az Azure AD Connect Health-ügynököket a célkiszolgálókon.

## Az Azure AD Connect Health-ügynök letöltése és telepítése

- Tekintse meg az Azure AD Connect Health használatának [előfeltételeit](active-directory-aadconnect-health-agent-install.md#Requirements).

- Az Azure AD Connect Health for AD FS használatának megkezdéséhez az ügynök legfrissebb verzióját ide kattintva töltheti le: [Az Azure AD Connect Health Agent for AD FS letöltése.](http://go.microsoft.com/fwlink/?LinkID=518973)
[](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

- A Azure AD Connect Health Agent for Sync használatának megkezdéséhez töltse le és telepítse az [Azure AD Connect legfrissebb verzióját](http://go.microsoft.com/fwlink/?linkid=615771).  Az állapotügynök az (1.0.9125.0-s verzió vagy újabb) Azure AD Connect részeként telepíthető.  Az Azure AD Connect támogatja az előző verziókról, helyben végzett frissítést.


## Az Azure AD Connect Health portál
Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat és használatelemzési információkat tekinthet meg. Az Azure AD Connect Health fő panelje a https://aka.ms/aadconnecthealth címen található.  A panelek az ablakoknak megfelelő funkciót töltenek be. A fő panelen a Gyors üzembe helyezés, az Azure AD Connect Health szolgáltatásai és további konfigurációs lehetőségek láthatók. A képernyőfelvételek alatt ezekről olvashat rövid magyarázatot.  Az ügynökök üzembe helyezését követően az állapotfigyelő szolgáltatás automatikusan azonosítja azokat a szolgáltatásokat, amelyeket az Azure AD Connect Health megfigyel.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/portal2.png)

- **Quick Start** (Gyors üzembe helyezés) – az elem kiválasztásával megnyílik a gyors üzembe helyezési panel. Itt a Get Tools (Eszközök beszerzése) lehetőség kiválasztásával letöltheti az Azure AD Connect Health-ügynököt, hozzáférhet a dokumentációkhoz, és visszajelzést is küldhet.

- **Active Directory Federation Services** (Active Directory összevonási szolgáltatások) – az Azure AD Connect Health által aktuálisan figyelt összes AD FS-szolgáltatás. Ha kiválaszt egy példányt, megnyílik egy, a szolgáltatáspéldányra vonatkozó információkat tartalmazó panel.  Az információk között tulajdonságokat, riasztásokat, megfigyelési adatokat, használatelemzést és egy áttekintést talál. A képességekről [itt](active-directory-aadconnect-health-adfs.md) talál további információkat.

- **Azure Active Directory Connect (Sync)** ((Szinkronizálási) Azure Active Directory Connect) – az Azure AD Connect Health által aktuálisan figyelt Azure AD Connect-kiszolgálók. Ha kiválaszt egy bejegyzést, megnyílik egy, az Azure AD Connect-kiszolgálókra vonatkozó információkat tartalmazó panel. A képességekről [itt](active-directory-aadconnect-health-sync.md) talál további információkat.

- **Configure** (Konfigurálás) – itt az alábbi funkciókat kapcsolhatja be vagy ki:

    1. Az automatikus frissítés engedélyezésével az Azure AD Connect Health-ügynök mindig a legfrissebb verzióban érhető el. Ez az jelenti, hogy az Azure AD Connect Health-ügynök automatikusan frissül a legfrissebb verzióra, amint az megjelenik. Ez e beállítás alapértelmezés szerint engedélyezve van.

    2. Engedélyezheti a Microsoft számára az Azure AD-címtár állapotadataihoz való hozzáférést hibaelhárítási céllal. Ez azt jelenti, hogy amikor a beállítás engedélyezve van, a Microsoft ugyanazokat az adatokat láthatja, amelyeket Ön. Ez a hibaelhárítás és a problémakezelés során jelent külön segítséget. A beállítás alapértelmezés szerint le van tiltva.


## Kapcsolódó hivatkozások

* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Using Azure AD Connect Health for Sync (Az Azure AD Connect Health for Sync használata)](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Jun16_HO2-->


