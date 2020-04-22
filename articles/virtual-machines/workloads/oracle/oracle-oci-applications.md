---
title: Architektúrák az Oracle-alkalmazások Azure virtuális gépeken való üzembe helyezéséhez | Microsoft dokumentumok
description: Alkalmazásarchitektúrák az Oracle-alkalmazások telepítéséhez, beleértve az E-Business Suite-ot, a JD Edwards EnterpriseOne-t és a PeopleSoft-ot az Azure-ban vagy az Oracle Cloud Infrastructure (OCI) adatbázisokkal rendelkező Microsoft Azure virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: f36dfe0092e3447053871ee0e5b4d659bb443779
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687486"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektúrák az Oracle-alkalmazások Azure-on való üzembe helyezéséhez

A Microsoft és az Oracle együttműködve lehetővé teszi az ügyfelek számára az Oracle-alkalmazások, például az Oracle E-Business Suite, a JD Edwards EnterpriseOne és a PeopleSoft felhőben történő telepítését. A Microsoft Azure és az Oracle Cloud Infrastructure (OCI) közötti előzetes [szintű magánhálózati összekapcsolhatóság](configure-azure-oci-networking.md) bevezetésével az Oracle-alkalmazások mostantól telepíthetők az Azure-ban az Azure-ban vagy az OCI-ben található háttér-adatbázisaikkal. Az Oracle-alkalmazások is integrálhatók az Azure Active Directoryval, így egyetlen bejelentkezést állíthat be, hogy a felhasználók az Azure Active Directory (Azure AD) hitelesítő adataikkal bejelentkezhessenek az Oracle-alkalmazásba.

Az OCI több Oracle adatbázis-beállítást kínál oracle-alkalmazásokhoz, például a DBaaS, az Exadata Cloud Service, az Oracle RAC és az Infrastructure-as-a-Service (IaaS) alkalmazásokhoz. Jelenleg az Autonomous Database nem támogatott háttérrendszer az Oracle alkalmazások számára.

Az Oracle-alkalmazások Azure-beli üzembe helyezésére több lehetőség is [rendelkezésre áll,](oracle-overview.md) többek között magas rendelkezésre állású és biztonságos módon. Az Azure is kínál [Oracle adatbázis virtuálisgép-lemezképek,](oracle-vm-solutions.md) amelyek üzembe helyezheti, ha úgy dönt, hogy futtassa az Oracle-alkalmazások teljes egészében az Azure-ban.

A következő szakaszok a Microsoft és az Oracle architektúrás javaslatait vázolják fel az Oracle E-Business Suite, a JD Edwards EnterpriseOne és a PeopleSoft felhőközi konfigurációban vagy teljes egészében az Azure-ban történő üzembe helyezéséhez. A Microsoft és az Oracle tesztelte ezeket az alkalmazásokat, és megerősítette, hogy a teljesítmény megfelel az Oracle által az alkalmazásokra meghatározott szabványoknak.

## <a name="architecture-considerations"></a>Építészeti szempontok

Az Oracle-alkalmazások több szolgáltatásból állnak, amelyek ugyanazon vagy több virtuális gépen üzemeltethetők az Azure-ban és opcionálisan az OCI-ben. 

Az alkalmazáspéldányok beállíthatók magán- vagy nyilvános végpontokkal. A Microsoft és az Oracle azt javasolja, hogy az alkalmazás kezeléséhez egy nyilvános IP-címmel rendelkező *megerősített gazdagép* beállítása egy külön alhálózatban. Ezután csak privát IP-címeket rendeljen a többi géphez, beleértve az adatbázisszintet is. 

Ha egy alkalmazást egy felhőközi architektúrában állít be, tervezésre van szükség annak biztosításához, hogy az Azure virtuális hálózat IP-címterülete ne fedje át az OCI virtuális felhőhálózat magánIP-címterületét.

A nagyobb biztonság érdekében állítson be hálózati biztonsági csoportokat alhálózati szinten, hogy csak az adott portokon és IP-címeken lévő forgalom engedélyezett legyen. Például a középső rétegben lévő gépek csak a virtuális hálózaton belüli forgalmat fogadhatnak. Egyetlen külső forgalom sem érheti el közvetlenül a középső rétegű gépeket.

A magas rendelkezésre állás érdekében beállíthatja a különböző kiszolgálók redundáns példányait ugyanabban a rendelkezésre állási csoportban vagy különböző rendelkezésre állási zónákban. A rendelkezésre állási zónák lehetővé teszik a 99,99%-os rendelkezésre állási SLA elérését, míg a rendelkezésre állási készletek lehetővé teszik a 99,95%-os rendelkezésre állási SLA elérését a régióban. Az ebben a cikkben látható mintaarchitektúrák két rendelkezésre állási zónára vannak telepítve.

Ha egy alkalmazást a felhőközi kapcsolat használatával telepít, továbbra is használhatja a meglévő ExpressRoute-áramkört az Azure-környezet helyszíni hálózathoz való csatlakoztatásához. Azonban szükség van egy külön ExpressRoute-kapcsolat az OCI-hoz való csatlakozáshoz, mint a helyszíni hálózathoz csatlakozó.

## <a name="e-business-suite"></a>E-Business lakosztály

Az Oracle E-Business Suite (EBS) egy alkalmazáscsomag, beleértve az ellátásilánc-menedzsmentet (SCM) és az ügyfélkapcsolat-kezelést (CRM). Az OCI felügyelt adatbázis-portfóliójának kihasználása érdekében az EBS a Microsoft Azure és az OCI közötti felhőközi kapcsolat használatával telepíthető. Ebben a konfigurációban a bemutató és az alkalmazásszintek az Azure-ban és az OCI adatbázisrétegében futnak, amint azt a következő architektúradiagram (1. ábra) is szemlélteti.

![E-Business Suite felhőközi architektúra](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*1. ábra: E-Business Suite felhőközi architektúra* 

Ebben az architektúrában az Azure virtuális hálózata az OCI virtuális felhőhálózatához csatlakozik a felhőközi összeköttetés használatával. Az alkalmazásszint az Azure-ban van beállítva, míg az adatbázis oci-ben van beállítva. Javasoljuk, hogy minden egyes összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokból érkező forgalmat engedélyezze bizonyos portokon.

Az architektúra teljes mértékben az Azure-on való üzembe helyezéshez is adaptálható, mivel az Oracle Data Guard használatával konfigurált, magas rendelkezésre állású Oracle-adatbázisok at konfigurálva egy régió két rendelkezésre állási zónájában. A következő ábra (2. ábra) egy példa erre az építészeti mintára:

![E-Business Suite csak Azure-t kínáló architektúra](media/oracle-oci-applications/ebs-arch-azure.png)

*2. ábra: E-Business Suite csak Azure-architektúra*

A következő szakaszok a különböző összetevőket magas szinten ismertetik.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Alkalmazás (középső) szint

Az alkalmazásszint a saját alhálózatában van elkülönítve. Több virtuális gép van beállítva a hibatűrésre és az egyszerű javításkezelésre. Ezeket a virtuális gépeket az Azure NetApp-fájlok és az Ultra SSD-k által kínált megosztott tárterület is támogathatja. Ez a konfiguráció lehetővé teszi a javítások egyszerűbb üzembe helyezését állásidő nélkül. Az alkalmazásszinten lévő gépeket egy nyilvános terheléselosztónak kell előre kezelnie, hogy az EBS alkalmazásszintre irányuló kérelmek feldolgozása akkor is meglegyen, ha a réteg egyik gépe hiba miatt offline állapotban van.

### <a name="load-balancer"></a>Terheléselosztó

Az Azure-terheléselosztó lehetővé teszi a forgalom elosztását a számítási feladatok több példányát a magas rendelkezésre állás biztosítása érdekében. Ebben az esetben egy nyilvános terheléselosztó van beállítva, mert a felhasználók hozzáférhetnek az EBS-alkalmazáshoz az interneten keresztül. A terheléselosztó a terhelést a középső réteg mindkét gépére osztja el. A nagyobb biztonság érdekében csak a vállalati hálózatról a vállalati hálózatról a rendszerbe jutó felhasználók tól engedélyezhet forgalmat a helyek közötti VPN- vagy ExpressRoute- vagy ExpressRoute-alapú és hálózati biztonsági csoportok használatával.

### <a name="database-tier"></a>Adatbázisszint

Ez a szint az Oracle-adatbázist üzemelteti, és a saját alhálózatára van elválasztva. Javasoljuk, hogy olyan hálózati biztonsági csoportokat adjon hozzá, amelyek csak az 1521-es Oracle-specifikus adatbázis-port adatbázis-szintjének az alkalmazásszintről az adatbázisrétegre irányuló forgalmat engedélyezik.

A Microsoft és az Oracle magas rendelkezésre állású beállítást javasol. Az Azure-ban magas rendelkezésre állás érhető el két Oracle-adatbázis két rendelkezésre állási zónában történő beállításával az Oracle Data Guard segítségével, vagy az Oracle Database Exadata Cloud Service használatával az OCI-ben. Az Oracle Database Exadata Cloud Service használata esetén az adatbázis két alhálózatban van telepítve. Az Oracle Database-t az OCI-ben lévő virtuális gépeken is beállíthatja két rendelkezésre állási tartományban az Oracle Data Guard segítségével.


### <a name="identity-tier"></a>Identitásszint

Az identitásszint tartalmazza az EBS Asserter virtuális gép. AZ EBS Asserter lehetővé teszi az identitások szinkronizálását az Oracle Identity Cloud Service (IDCS) és az Azure AD szolgáltatásból. Az EBS Asserterre azért van szükség, mert az EBS nem támogatja az olyan egyszeri bejelentkezési protokollokat, mint az SAML 2.0 vagy az OpenID Connect. The EBS Asserter consumes the OpenID connect token (generated by IDCS), validates it, and then creates a session for the user in EBS. 

Bár ez az architektúra az IDCS-integrációt mutatja, az Azure AD egyesített hozzáférése és egyszeri bejelentkezése az Oracle Access Manager segítségével is engedélyezhető az Oracle Internet Directory vagy az Oracle Unified Directory segítségével. További információt az [Oracle EBS IDCS-integrációval történő telepítéséről](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) vagy az [Oracle EBS OAM-integrációval történő központi telepítéséről](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)szóló tanulmányban talál.

A magas rendelkezésre állás érdekében ajánlott az EBS Asserter redundáns kiszolgálóit több rendelkezésre állási zónára telepíteni, előtte egy terheléselosztóval.

Az infrastruktúra beállítása után az E-Business Suite az Oracle által biztosított telepítési útmutató tkövetően telepíthető.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards Vállalat

Az Oracle JD Edwards EnterpriseOne egy integrált alkalmazások csomag átfogó vállalati erőforrás-tervezési szoftver. Ez egy többrétegű alkalmazás, amely oracle vagy SQL Server adatbázis-háttérrendszerrel is beállítható. Ez a szakasz a JD Edwards EnterpriseOne egy Oracle-adatbázis-háttérrendszerrel való üzembe helyezésének részleteit ismerteti oci-ben vagy az Azure-ban.

A következő ajánlott architektúrában (3. ábra) a felügyeleti, a bemutató és a középső rétegek az Azure-beli virtuális hálózatra kerülnek. Az adatbázis az OCI virtuális felhőhálózatában van telepítve.

Az E-Business Suite-hoz is beállíthat egy opcionális megerősített réteget biztonságos felügyeleti célokra. Használja a bástya virtuálisgép-gazdagép ugrókiszolgálóként az alkalmazás és az adatbázispéldányok eléréséhez.

![JD Edwards EnterpriseOne felhőközi architektúra](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*3. ábra: JD Edwards EnterpriseOne felhőközi architektúra*

Ebben az architektúrában az Azure virtuális hálózata csatlakozik a virtuális felhőhálózatoci a felhőközi összekötő használatával. Az alkalmazásszint az Azure-ban van beállítva, míg az adatbázis oci-ben van beállítva. Javasoljuk, hogy minden egyes összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokból érkező forgalmat engedélyezze bizonyos portokon.

Az architektúra teljes mértékben az Azure-on való üzembe helyezéshez is adaptálható, mivel az Oracle Data Guard használatával konfigurált, magas rendelkezésre állású Oracle-adatbázisok at konfigurálva egy régió két rendelkezésre állási zónájában. A következő ábra (4. ábra) egy példa erre az építészeti mintára:

![JD Edwards EnterpriseOne csak Azure-t leveged én architektúra](media/oracle-oci-applications/jdedwards-arch-azure.png)

*4. ábra: JD Edwards EnterpriseOne csak Azure-architektúra*

A következő szakaszok a különböző összetevőket magas szinten ismertetik.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Felügyeleti szint

Ahogy a neve is sugallja, ez a szint felügyeleti feladatokhoz használatos. A felügyeleti szinthez külön alhálózatot is kiválaszthat. Az ebben a rétegben található szolgáltatásokat és kiszolgálókat elsősorban az alkalmazás telepítéséhez és felügyeletéhez használják. Ezért ezeknek a kiszolgálóknak az egyetlen példánya elegendő. Redundáns példányok nem szükségesek az alkalmazás magas rendelkezésre állásához.

A szint összetevői a következők:
    
 - **Kiépítési kiszolgáló** – Ez a kiszolgáló az alkalmazás különböző összetevőinek végpontok között történő telepítéséhez használatos. Kommunikál a példányok a többi rétegben, beleértve a példányok az adatbázisrétegben, a 22-es porton keresztül. Ez a dajtadt a JD Edwards EnterpriseOne kiszolgálókezelő konzolja.
 - **Központi telepítési kiszolgáló** – Ez a kiszolgáló elsősorban a JD Edwards EnterpriseOne telepítéséhez szükséges. A telepítési folyamat során ez a kiszolgáló a szükséges fájlok és telepítőcsomagok központi tárházaként működik. A szoftver a kiszolgálóról más kiszolgálókra és ügyfelekre kerül.
 - **Fejlesztői ügyfél** – Ez a kiszolgáló webböngészőben futó összetevőket és natív alkalmazásokat tartalmaz.

### <a name="presentation-tier"></a>Bemutatási réteg

Ez a szint különböző összetevőket tartalmaz, például az Alkalmazásfelület-szolgáltatások (AIS), az Alkalmazásfejlesztési keretrendszer (ADF) és a Java application servers (JAS). A réteg kiszolgálói kommunikálnak a középső rétegkiszolgálóival. Ezeket egy terheléselosztó vezeti előre, amely a forgalmat a szükséges kiszolgálóra irányítja a portszám és az URL-cím alapján, amelyen a forgalom érkezik. A magas rendelkezésre állás érdekében ajánlott minden kiszolgálótípusból több példányt telepíteni.

A következő összetevők találhatók ebben a rétegben:
    
- **Application Interface Services (AIS)** - Az AIS-kiszolgáló biztosítja a kommunikációs interfészt a JD Edwards EnterpriseOne mobil vállalati alkalmazások és a JD Edwards EnterpriseOne között.
- **Java Application Server (JAS)** – A JAS kéréseket fogad a terheléselosztótól, és átadja azt a középső rétegnek bonyolult feladatok végrehajtásához. A JAS képes egyszerű üzleti logikát végrehajtani.
- **BI Publisher Server (BIP)** – Ez a kiszolgáló a JD Edwards EnterpriseOne alkalmazás által gyűjtött adatok alapján mutatja be a jelentéseket. Megtervezheti és szabályozhatja, hogy a jelentés hogyan jelenítse meg az adatokat a különböző sablonok alapján.
- **Business Services Server (BSS)** – A BSS lehetővé teszi az információcserét és az interoperabilitást más Oracle alkalmazásokkal.
- **Valós idejű eseménykiszolgáló (RTE)** – Az RTE-kiszolgáló lehetővé teszi, hogy a JDE EnterpriseOne rendszerben előforduló tranzakciókról értesítéseket állítson be külső rendszereknek. Előfizetői modellt használ, és lehetővé teszi, hogy harmadik fél től származó rendszerek feliratkozzanak az eseményekre. Ha mindkét RTE-kiszolgálónak be szeretné tölteni az elosztási kérelmeket, győződjön meg arról, hogy a kiszolgálók fürtben vannak.
- **Alkalmazásfejlesztési keretrendszer (ADF) kiszolgáló** – Az ADF-kiszolgáló az Oracle ADF-fel kifejlesztett JD Edwards EnterpriseOne alkalmazások futtatására szolgál. Ez egy ADF-futásidejű Oracle WebLogic kiszolgálón van telepítve.

### <a name="middle-tier"></a>Középső szint

A középső réteg tartalmazza a logikai kiszolgálót és a kötegkiszolgálót. Ebben az esetben mindkét kiszolgáló ugyanazon a virtuális gépen van telepítve. Éles környezetben azonban ajánlott a logikai és kötegkiszolgálót külön kiszolgálókra telepíteni. Több kiszolgáló van telepítve a középső rétegben két rendelkezésre állási zónában a magasabb rendelkezésre állás érdekében. Létre kell hozni egy Azure-terheléselosztót, és ezeket a kiszolgálókat a háttérkészletébe kell helyezni annak érdekében, hogy mindkét kiszolgáló aktív és feldolgozási kérelmek.

A középső rétegkiszolgálói csak a megjelenítési szint és a nyilvános terheléselosztó kiszolgálóitól kapnak kéréseket. A hálózati biztonsági csoport szabályait úgy kell beállítani, hogy a bemutatószint alhálózatán és a terheléselosztón kívül más címről származó forgalmat is megtiltsák. NSG-szabály is beállítható, hogy a 22-es porton a 22-es porton a cástétú állomásról felügyeleti célokra engedélyezze a forgalmat. Előfordulhat, hogy a nyilvános terheléselosztó használatával a középső rétegben lévő virtuális gépek közötti terheléselosztási kérelmeket használhatja.

A középső rétegben a következő két összetevő található:

- **Logikai kiszolgáló** – Üzleti logikát vagy üzleti függvényeket tartalmaz.
- **Kötegkiszolgáló** – kötegelt feldolgozásra szolgál

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

Az Oracle PeopleSoft alkalmazáscsomagja humán erőforrás- és pénzgazdálkodási szoftvereket tartalmaz. Az alkalmazáscsomag többszintű, és az alkalmazások közé tartozik a humánerőforrás-gazdálkodási rendszerek (HRMS), ügyfélkapcsolat-kezelés (CRM), pénzügyi és ellátási lánc menedzsment (FSCM), és a vállalati teljesítménymenedzsment (EPM).

Javasoljuk, hogy a szoftvercsomag minden rétegét a saját alhálózatában telepítse. Oracle-adatbázis vagy Microsoft SQL Server szükséges az alkalmazás háttér-adatbázisaként. Ez a szakasz a PeopleSoft Oracle-adatbázis-háttér-háttér-háttér-kezelővel történő telepítésének részleteit ismerteti.

A következő egy kanonikus architektúra a PeopleSoft alkalmazáscsomag felhőközi architektúrában való üzembe helyezéséhez (5. ábra).

![PeopleSoft felhőközi architektúra](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*5. ábra: PeopleSoft felhőközi architektúra*

Ebben a mintaarchitektúrában az Azure virtuális hálózata csatlakozik a virtuális felhőhálózatoci a felhőközi összekötő használatával. Az alkalmazásszint az Azure-ban van beállítva, míg az adatbázis oci-ben van beállítva. Javasoljuk, hogy minden egyes összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokból érkező forgalmat engedélyezze bizonyos portokon.

Az architektúra teljes mértékben az Azure-on való üzembe helyezéshez is adaptálható, mivel az Oracle Data Guard használatával konfigurált, magas rendelkezésre állású Oracle-adatbázisok at konfigurálva egy régió két rendelkezésre állási zónájában. A következő ábra (6. ábra) egy példa erre az építészeti mintára:

![Csak PeopleSoft Azure-t lezáró architektúra](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*6. ábra: Csak PeopleSoft Azure-architektúra*

A következő szakaszok a különböző összetevőket magas szinten ismertetik.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Alkalmazásréteg

Az alkalmazásszint a PeopleSoft alkalmazáskiszolgálók, a PeopleSoft webkiszolgálók, a rugalmas keresés és a PeopleSoft folyamatütemező példányait tartalmazza. Az Azure-terheléselosztó úgy van beállítva, hogy fogadja a felhasználók kéréseit, amelyek az alkalmazáscsomag megfelelő kiszolgálójára vannak irányítva.

A magas rendelkezésre állás érdekében fontolja meg az alkalmazásréteg egyes kiszolgálóinak redundáns példányainak beállítását a különböző rendelkezésre állási zónák között. Az Azure terheléselosztó több háttérkészlettel is beállítható, hogy minden egyes kérést a megfelelő kiszolgálóra irányítson.

### <a name="peopletools-client"></a>PeopleTools ügyfél

A PeopleTools ügyfél felügyeleti tevékenységek, például fejlesztés, áttelepítés és frissítés végrehajtására szolgál. Mivel a PeopleTools ügyfél nem szükséges az alkalmazás magas rendelkezésre állásának eléréséhez, nincs szükség a PeopleTools ügyfél redundáns kiszolgálóira.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>További lépések

[Terraform-parancsfájlok](https://github.com/microsoft/azure-oracle) használatával állíthatja be az Oracle-alkalmazásokat az Azure-ban, és hozhat létre felhőközi kapcsolatot az OCI-vel.

Az OCI-ről további információt és tanulmányt az [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentációjában talál.
