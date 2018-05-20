---
title: Service Fabric-fürt létrehozása az Azure portálon |} Microsoft Docs
description: A cikkből megtudhatja, hogyan állíthat be az Azure-ban az Azure portál és az Azure Key Vault biztonságos Service Fabric-fürt.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/09/2018
ms.author: aljo
ms.openlocfilehash: fbe3dde48fa1be79356ff1cb9e47c46832d8cdaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>A Service Fabric-fürt létrehozása az Azure-ban az Azure-portálon
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ez a cikk részletesen ismerteti, amely végigvezeti a lépéseken, hogyan kell beállítani a Service Fabric-fürt (Linux- vagy Windows-) az Azure-ban az Azure-portálon. Ez az útmutató végigvezeti az alábbi lépéseket:

* Fürt létrehozása az Azure-ban az Azure portálon keresztül.
* Rendszergazdák tanúsítványok segítségével hitelesíti.

> [!NOTE]
> A speciális biztonsági beállítások, például a felhasználói hitelesítés az Azure Active Directory és az alkalmazás biztonsági tanúsítványok beállítását [Azure Resource Manager használatával a fürt létrehozása][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Fürtbiztonság 
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. További információ a tanúsítványok használatának módját a Service Fabric: [Service Fabric-fürt biztonsági forgatókönyvek][service-fabric-cluster-security].

Ha első alkalommal létrehozni a service fabric-fürt vagy a fürt test munkaterheléseknél telepíti, továbbléphet a következő szakaszban a (**fürt létrehozása az Azure portálon**) és a rendszer a szükséges tanúsítványok létrehozása a tesztelési feladatokat futtató fürtöket. Ha beállít egy fürt termelési számítási feladatokhoz, majd folytassa a olvasásakor.

#### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Ez a tanúsítvány szükséges biztosításához a fürt és a jogosulatlan hozzáférés elkerülése érdekében azt. Fürt biztonsági néhány módon tartalmazza:

* **Fürt hitelesítési:** hitelesíti a fürt összevonási csomópontok kommunikációt. Csak olyan csomópontot, amely bizonyítja, ezzel a tanúsítvánnyal az identitásukat csatlakozhat a fürthöz.
* **Kiszolgálóhitelesítés:** hitelesíti a felügyeleti ügyfél, a fürt felügyeleti végpontokat, így a felügyeleti ügyfél tudja azt a valódi fürthöz van szó. Ez a tanúsítvány is biztosít SSL a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül.

Ezeket a célokat szolgál, hogy a tanúsítvány a következő követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható léteznie kell a tanúsítványt.
* A tanúsítvány **tanúsítványtulajdonos nevének egyeznie kell a tartomány** a Service Fabric-fürt eléréséhez használt. Ez feltétele SSL biztosít a fürt HTTPS felügyeleti végpontok és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a `.cloudapp.azure.com` tartomány. Szerezzen be egy egyéni tartománynevet a fürt számára. Ha tanúsítványt kérhet egy hitelesítésszolgáltató a tanúsítvány tulajdonosának nevét meg kell egyeznie a fürt használja ezeket az egyéni tartománynév.

#### <a name="client-authentication-certificates"></a>Ügyfél-hitelesítési tanúsítványok
További ügyfél-tanúsítványok fürt felügyeleti feladatok rendszergazdák hitelesítéséhez. A Service Fabric két olyan hozzáférési szintje van: **admin** és **írásvédett felhasználó**. Legalább a rendszergazdai hozzáférést a rendszer egy tanúsítványt kell használni. A további felhasználói szintű hozzáférés érdekében egy külön tanúsítványt kell megadni. A hozzáférés szerepkörök további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric ügyfelek][service-fabric-cluster-security-roles].

Nem kell együttműködni a Service Fabric Key Vault ügyfél-hitelesítési tanúsítványok feltöltése. Meg kell adni a felhasználók számára, akik jogosultak a fürtkezeléshez csak kell ezeket a tanúsítványokat. 

> [!NOTE]
> Az Azure Active Directory használatával hitelesíti az ügyfeleket a fürtfelügyeleti műveleteket ajánlott módja. Az Azure Active Directoryt, le kell [hozzon létre egy fürtöt, Azure Resource Manager használatával][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványokat is telepíthető egy fürt biztonsági okokból. Az fürt létrehozása előtt fontolja meg az alkalmazás biztonsági kell telepíteni a csomópontokat, például a tanúsítványt igénylő forgatókönyvek:

* Az alkalmazás konfigurációs értékeit titkosítását és visszafejtését
* Replikáció során az adatok csomópontok közötti titkosítása 

Tanúsítványok nem állítható be, amikor az Azure portálon keresztül egy fürtöt hoz létre. Tanúsítványok konfigurálása a fürt telepítéskor, kell [hozzon létre egy fürtöt, Azure Resource Manager használatával][create-cluster-arm]. Azt is megteheti alkalmazástanúsítványokra a fürt létrehozása után.

< /a "létrehozása, fürt, portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Fürt létrehozása az Azure-portálon

Az alkalmazás igényeinek éles fürt létrehozása magában foglalja a néhány tervezési segítséget nyújt, amely, erősen ajánlott, hogy elolvasta és megértette [tervezési megfontolások a Service Fabric-fürt] [ service-fabric-cluster-capacity] dokumentum. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Keresse meg a Service Fabric-fürt erőforrás
![Keresse meg a Service Fabric fürt sablont az Azure portálon.][SearchforServiceFabricClusterTemplate]

1. Jelentkezzen be az [Azure Portalra][azure-portal].
2. Kattintson a **hozzon létre egy erőforrást** új erőforrás sablon hozzáadását. Keresse meg a Service Fabric-fürt-sablon a **piactér** alatt **mindent**.
3. Válassza ki **Service Fabric-fürt** a listából.
4. Keresse meg a **Service Fabric-fürt** panelen kattintson a **létrehozása**,
5. A **létrehozása a Service Fabric-fürt** panel rendelkezik a következő négy lépést:

#### <a name="1-basics"></a>1. Alapvető beállítások
![Képernyőfelvétel az új erőforráscsoport létrehozásához.][CreateRG]

Az alapvető beállítások panel meg kell adnia az alapvető adatokat a fürt számára.

1. Adja meg a fürt nevét.
2. Adjon meg egy **felhasználónév** és **jelszó** a virtuális gépekhez távoli asztal.
3. Ügyeljen arra, hogy válassza ki a **előfizetés** , amelyet a fürt üzembe helyezni, különösen akkor, ha több előfizetéssel rendelkezik.
4. Hozzon létre egy **új erőforráscsoport**. A legcélszerűbb adjon neki a neve megegyezik a fürt, mivel segíti a keresés, azokat később, különösen akkor, ha módosítja a központi telepítés, vagy törölje a fürtöt, kívánt.
   
   > [!NOTE]
   > Eldöntheti, hogy egy meglévő erőforráscsoportot használni, de ajánlott hozzon létre egy új erőforráscsoportot. Ez megkönnyíti, hogy törli a fürtök és az összes erőforrást használ.
   > 
   > 
5. Válassza ki a **régió** a kívánja a fürt létrehozásához. Ha azt tervezi, hogy már feltöltött kulcstároló meglévő tanúsítvány használatára, ugyanabban a régióban, amely a Key vault kell használnia. 

#### <a name="2-cluster-configuration"></a>2. Fürtkonfiguráció
![Csomópont-típus létrehozása][CreateNodeType]

Konfigurálja a fürtcsomópontokat. Csomóponttípusok a Virtuálisgép-méretek, a virtuális gépek számát és azok tulajdonságait határozza meg. A fürt több csomóponttípus is rendelkezik, de az elsődleges csomóponttípusok (az első egy Ön által a portál), ez a csomópont típusa ahol kerülnek, a Service Fabric rendszerszolgáltatások kell rendelkeznie legalább öt virtuális gépeket. Ne konfiguráljon **elhelyezési tulajdonságok** , mert a rendszer automatikusan hozzáadja egy alapértelmezett elhelyezési "NodeTypeName" tulajdonságát.

> [!NOTE]
> Egy általános forgatókönyv több csomópont esetében az előtér-szolgáltatás és a háttér-szolgáltatás tartalmazó alkalmazást. Az előtér-szolgáltatás elhelyezése kisebb rendelkező virtuális gépek (VM-méretek D2_V2 hasonlóan) portok, nyissa meg az internethez, és hogy a háttér-szolgáltatás a nagyobb virtuális gépek (VM-méretek például D3_V2, D6_V2, D15_V2 és így tovább) internetre portot szeretne.
> 
> 

1. Adjon nevet a csomóponttípus (1 – 12 csak betűket és számokat tartalmazó karakter).
2. A minimális **mérete** az elsődleges csomóponthoz tartozó virtuális gépek típus által vezérelt a **tartóssági** réteg úgy dönt, hogy a fürt számára. A tartóssági szint alapértelmezés szerint bronz. A durability további információkért lásd: [kiválasztása a Service Fabric-fürt tartóssági][service-fabric-cluster-durability].
3. Válassza ki a Virtuálisgép-méretet. D sorozatú virtuális gépek SSD meghajtók rendelkeznek, és erősen ajánlott állapotalapú alkalmazásokhoz. Nem használja a virtuális gép SKU részleges maggal rendelkező vagy 10 GB-nál kevesebb szabad kapacitását. Tekintse meg [tervezési szempont a dokumentum a service fabric-fürt] [ service-fabric-cluster-capacity] segítségre van a Virtuálisgép-méret kiválasztása.
4. Válassza ki a virtuális gépek számát a csomóponttípus. Méretezheti felfelé vagy lefelé a virtuális gépek számát csomóponttípus később, de az elsődleges csomóponttípuson legalább öt termelési számítási feladatokhoz. Egyéb legalább egy virtuális gép is. A minimális **szám** virtuális gépek az elsődleges csomópont típus esetén a **megbízhatóság** a fürt.  
5. **Fürt csomópont és három csomópontos fürt egyetlen** -ezek célja csak tesztelési célokra. Ezek nem támogatottak a minden futó termelési számítási feladatokhoz.
6. Egyéni végpontokat konfigurálhat. Ez a mező lehetővé teszi, hogy meg kell adnia a Azure Load Balancer az alkalmazások a nyilvános interneten keresztül elérhetővé tenni kívánt portok vesszővel tagolt listája. Például ha azt tervezi, a webalkalmazások a fürt telepítéséhez, adja meg "80" Itt forgalmat engedélyezi a 80-as portot a fürtbe. A végpontok további információkért lásd: [alkalmazások folytatott kommunikáció][service-fabric-connect-and-communicate-with-services]
7. Fürt konfigurálása **diagnosztika**. Alapértelmezés szerint diagnosztika engedélyezve vannak a fürtön lévő segít a problémák elhárításához. Ha le szeretné tiltani a diagnosztika módosítása a **állapot** kapcsolót **ki**. Diagnosztika kikapcsolása van **nem** ajánlott. Ha már létrehozott Application Insights-projektet, adja meg annak a kulcsát, hogy az alkalmazások nyomkövetéseit legyenek átirányítva.
8. Válassza ki a kívánt beállítása a fürt háló frissítési módot. Válassza ki **automatikus**, ha azt szeretné, hogy a rendszer automatikusan felveszi az elérhető legújabb verzióra, majd próbálja meg a fürt frissítéséhez. Beállíthatja a módot **manuális**, ha meg kívánja valamelyik támogatott verzióra. A további részleteket a hálót a frissítési mód lásd: a [service fabric-fürt-frissítési dokumentum.][service-fabric-cluster-upgrade]

> [!NOTE]
> Csak a service Fabric támogatott verzióit futtató fürtök támogatott. Kiválasztásával a **manuális** módban készítésének felelősségére a fürt valamelyik támogatott verzióra frissítéséhez. > 
> 

#### <a name="3-security"></a>3. Biztonság
![Képernyőfelvétel az Azure-portál biztonsági beállításokkal.][BasicSecurityConfigs]

A biztonságos tesztfürthöz beállításának könnyen elvégzésére, adtunk a **alapvető** lehetőséget. Ha már rendelkezik egy tanúsítvánnyal rendelkezik fel van töltve, a keyvault (és engedélyezve van a központi telepítés a key vault), majd használja a **egyéni** beállítás

##### <a name="basic-option"></a>Alapszintű beállítás
Kövesse a képernyőn hozzáadása vagy egy meglévő keyvault felhasználhatja és tanúsítvány hozzáadásához. A tanúsítvány hozzáadása szinkron eljárás, és úgy kell várja meg a tanúsítványt létrehozni.


A képernyő navigál, amíg az előző folyamat befejeződött, a kísértésnek ellenáll.

![CreateKeyVault]

Most, hogy a tanúsítványt a keyvault ad hozzá, a rendszer kérni fogja a hozzáférési házirendek szerkesztése a Keyvault a következő képernyő jelenhet meg. Kattintson a **a hozzáférési házirend szerkesztése.** gombra.

![CreateKeyVault2]

Kattintson a speciális hozzáférési házirendek szerint, és engedélyezze a hozzáférést a virtuális gépek telepítése. Javasoljuk, hogy a sablon a központi telepítést engedélyeznie is. Ha létrejött a beállításokat, akkor ne feledje el, kattintson a **mentése** gombra, majd zárja be a a **hozzáférési házirendek** ablaktáblán.

![CreateKeyVault3]

Most már készen áll a folytatáshoz ki a fürt létrehozása folyamat.

![CreateKeyVault4]

##### <a name="custom-option"></a>Egyéni beállítást
Ebben a szakaszban hagyja ki, ha már elvégezte a lépéseket a **alapvető** lehetőséget.

![SecurityCustomOption]

A CertificateThumbprint, SourceVault és a CertificateURL adatokat, a biztonság lapon befejezéséhez szükséges. Ha nincs kéznél legyen, nyissa meg a másik böngészőablakot, és tegye a következőket


1. Keresse meg a keyvault, válassza ki azt a tanúsítványt. 
2. Válassza ki a "Tulajdonságok" lapon, és másolja erőforrás-azonosító "Forrás Key vault" más böngésző ablakban 

    ![CertInfo0]

3. Most válassza a "Tanúsítványok" lapot.
4. Kattintson a tanúsítvány-ujjlenyomat, amely verziók oldalára viszi.
5. Kattintson a jelenlegi verzió alatt látható GUID.

    ![CertInfo1]

6. Kell a például az alábbi képernyőképen. Másolja az ujjlenyomatot "Tanúsítvány ujjlenyomata" (a böngészőablakot
7. Az "Secret azonosító" a "tanúsítvány URL-cím" más böngészőablakban adatokat másolni.


![CertInfo2]


Ellenőrizze a **speciális beállítások konfigurálása** mezőben adja meg az ügyféltanúsítványok **rendszergazdai ügyfél** és **írásvédett ügyfél**. Ezekben a mezőkben adja meg a rendszergazdai ügyféltanúsítvány ujjlenyomata és az írásvédett felhasználó ügyféltanúsítvány ujjlenyomata, ha van ilyen. Ha a rendszergazdák próbál csatlakozzon a fürthöz, kapnak itt megadott hozzáférés csak akkor, ha rendelkeznek, amely megfelel az ujjlenyomat-értékeket egy ujjlenyomattal rendelkező tanúsítványt.  

#### <a name="4-summary"></a>4. Összegzés

Most már készen áll a fürt telepítéséhez. Mielőtt ezt megtenné, töltse le a tanúsítványt, keresse meg a hivatkozás nagy kék tájékoztató mezőn belül. Győződjön meg arról, hogy a tanúsítvány tartsa biztonságos helyen. esetleg szükség lenne rá kapcsolódik a fürthöz. A letöltött tanúsítvány nem tartozik jelszó, javasoljuk, hogy akkor vegyen fel egyet.

A fürt létrehozásának befejezéséhez kattintson a **létrehozása**. A sablon opcionálisan letölthető. 

![Összegzés]

A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a „Harang” ikonra az állapotsor mellett, a képernyő jobb felső részén.) Ha a fürt létrehozásakor **A kezdőpulton rögzít** lehetőségre kattintott, a **Service Fabric-fürt üzembe helyezése** a **Kezdőpultra** rögzítve látható.

További kezelési műveleteket szeretne végrehajtani a fürtön, a Powershell vagy a parancssori felület, csatlakozzon a fürthöz kell, módjáról [csatlakozni a fürthöz](service-fabric-connect-to-secure-cluster.md).

### <a name="view-your-cluster-status"></a>A fürt állapotának megtekintése
![Képernyőfelvétel a fürt részletes adatai az irányítópulton.][ClusterDashboard]

A fürt létrehozása után vizsgálhatja meg a fürt a portálon:

1. Ugrás a **Tallózás** kattintson **Service Fabric-fürtök**.
2. Keresse meg a fürthöz, és kattintson rá.
3. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

A **csomópont Monitor** szakasz a fürt irányítópult panelen, amelyek a megfelelő és nem kifogástalan állapotú virtuális gépek számát jelzi. A fürt állapotát, további információkat is talál [Service Fabric rendszerállapot-modell bemutatása][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric-fürtök egy bizonyos megkövetelése a csomópontok kell mentése mindig rendelkezésre álljon, állapot - néven "kvórum fenntartása" megőrzéséhez. Emiatt nincs általában biztonságos a fürt összes gépek leállítását, kivéve, ha először elvégezte a [teljes biztonsági mentés a állapot][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás egy virtuálisgép-méretezési csoport példányt vagy egy fürt csomópontja
A NodeType tulajdonságok értéke mindegyikének ad meg a fürt eredmény egy virtuálisgép-méretezési csoportban lévő első beállításról. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>További lépések
Ezen a ponton hogy a biztonságos fürt felügyeleti hitelesítési tanúsítványokat használnak. Ezt követően [csatlakozzon a fürthöz](service-fabric-connect-to-secure-cluster.md) és megtudhatja, hogyan [alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).  Emellett megismerése [Service Fabric támogatási lehetőségek](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Összefoglalás]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
