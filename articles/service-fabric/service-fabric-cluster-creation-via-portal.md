---
title: Service Fabric-fürt létrehozása az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure-ban az Azure portal és az Azure Key Vault biztonságos Service Fabric-fürtön.
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
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 71448125d7308ca28e7241fd4019aadba430214e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106137"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Service Fabric-fürt létrehozása az Azure-ban az Azure portal használatával
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ez az egy lépésenkénti útmutató, amely végigvezeti a lépéseken, beállítása a Service Fabric-fürt (Linux vagy Windows) az Azure-ban az Azure portal használatával. Ez az útmutató végigvezeti az alábbi lépéseket:

* Fürt létrehozása az Azure-ban az Azure Portalon keresztül.
* A rendszergazdák tanúsítványok használatával hitelesíteni.

> [!NOTE]
> A speciális biztonsági beállításai, például a felhasználói hitelesítés az Azure Active Directory és a tanúsítványok alkalmazásbiztonsági beállításának [létrehozhat egy fürtöt az Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Fürtbiztonság 
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. További információ a tanúsítványok használata a Service Fabric: [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security].

Ha első alkalommal hoz létre egy service fabric-fürt, vagy a tesztelési feladatok futtatásához egy fürtöt telepít, továbbléphet a következő szakaszban a (**fürt létrehozása az Azure Portalon**) és a rendszer a szükséges tanúsítványok előállítása a tesztelési feladatokat futtató fürtöket. Ha egy fürtöt a termelési számítási feladatokhoz, majd olvassa tovább.

#### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Fürt biztonságossá tétele és a jogosulatlan hozzáférés elkerülése érdekében, hogy ez a tanúsítvány szükséges. Fürtbiztonság néhány módon biztosít:

* **Fürt-hitelesítés:** Csomópontok közötti kommunikáció a fürt összevonási hitelesíti. Csak a is igazolnia az identitását, ezt a tanúsítványt a csomópontok csatlakozhassanak a fürthöz.
* **Kiszolgálói hitelesítés:** Hitelesíti a fürtkezelési végpontoknak egy kezelési ügyfél, így az ügyfél felismeri, hogy ez a valódi fürtön beszél. Ezt a tanúsítványt is biztosít az SSL a HTTPS-felügyeleti API-hoz és a Service Fabric Explorert a HTTPS-kapcsolaton keresztül.

A tanúsítvány következő célokat, az alábbi követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható a tanúsítványt kell létrehozni.
* A tanúsítvány **tulajdonosnévnek egyeznie kell a tartomány** a Service Fabric-fürt eléréséhez használt. Ez azért szükséges, adja meg az SSL a fürt HTTPS-felügyeleti végpontjai és a Service Fabric Explorer. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) nem szerezheti be a `.cloudapp.azure.com` tartományhoz. Szerzik be a fürthöz tartozó egyéni tartomány nevét. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól a tanúsítvány tulajdonosnevének egyeznie kell az a fürthöz használt egyéni tartománynév.

#### <a name="client-authentication-certificates"></a>Ügyfél-hitelesítési tanúsítványok
További ügyféltanúsítványok hitelesítése a rendszergazdák a fürt felügyeleti feladatokhoz. A Service Fabric két hozzáférési szintekkel rendelkezik: **rendszergazdai** és **olvasási jogosultsággal rendelkező felhasználó**. Legalább egy tanúsítvány rendszergazdai hozzáféréshez kell használni. A további felhasználói szintű hozzáférés érdekében a különálló tanúsítványt meg kell adni. Hozzáférés szerepkörökkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek][service-fabric-cluster-security-roles].

Nem kell az ügyfél-hitelesítési tanúsítványok feltöltése a Key Vault használata a Service Fabric. Ezek a tanúsítványok csak kell adni a felhasználók számára, akik jogosultak a fürt felügyeletéhez. 

> [!NOTE]
> Az Azure Active Directory ajánlott módja a fürtfelügyeleti műveleteket az ügyfelek hitelesítéséhez. Azure Active Directory használandó kell [hozzon létre egy fürtöt az Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványok telepíthető alkalmazás biztonsági okokból egy fürtön. Mielőtt létrehozná a fürtöt, vegye figyelembe a kell telepíteni a csomópontokon, például a tanúsítványt igénylő alkalmazásbiztonsági forgatókönyveket:

* Az alkalmazás konfigurációs értékek titkosítását és visszafejtését
* Az adatok titkosítása az csomópont közötti replikálás során 

Tanúsítványok nem lehet konfigurálni, mikor [egy fürtöt az Azure Portalon keresztül](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Fürt telepítéskor tanúsítványok konfigurálásához kell [hozzon létre egy fürtöt az Azure Resource Manager][create-cluster-arm]. Is adhat tanúsítványok a fürt létrehozása után.

## <a name="create-cluster-in-the-azure-portal"></a>Fürt létrehozása az Azure Portalon

Az alkalmazás igényeinek egy éles fürtöt magában foglalja a néhány tervezési segítséget nyújt, amely, erősen ajánlott, hogy Ön elolvassa és megértse az [tervezési megfontolások a Service Fabric-fürt] [ service-fabric-cluster-capacity] dokumentumot. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>A Service Fabric-fürt erőforrás keresése

Jelentkezzen be az [Azure Portalra][azure-portal].
Kattintson a **erőforrás létrehozása** egy új erőforrás-sablon hozzáadása. Keresse meg a Service Fabric-fürt-sablon a **Marketplace** alatt **mindent**.
Válassza ki **Service Fabric-fürt** a listából.

![Keresse meg a Service Fabric-fürtöket az Azure Portalon.][SearchforServiceFabricClusterTemplate]

Keresse meg a **Service Fabric-fürt** panelen, és kattintson **létrehozás**.

A **létrehozása a Service Fabric-fürt** panelen a következő négy lépésből áll:

### <a name="1-basics"></a>1. Alapvető beállítások
![Képernyőfelvétel az új erőforráscsoport létrehozásához.][CreateRG]

Az alapvető beállítások panelen meg kell adnia az alapvető adatokat a fürt számára.

1. Adja meg a fürt nevére.
2. Adjon meg egy **felhasználónév** és **jelszó** a távoli asztal a virtuális gépek számára.
3. Ügyeljen arra, hogy válassza ki a **előfizetés** , amelyet a fürt üzembe helyezni, különösen akkor, ha több előfizetéssel rendelkezik.
4. Hozzon létre egy új **erőforráscsoport**. A legcélszerűbb neki a neve megegyezik a fürt, mivel ez segít a keresés, azokat később, különösen akkor, ha módosítja az üzemelő példány, vagy törölje a fürtöt szeretne.
   
   > [!NOTE]
   > Bár Ön dönt, hogy használjon egy meglévő erőforráscsoportot, tanácsos hozzon létre egy új erőforráscsoportot. Így művelettel törölheti a fürtök és az összes erőforrást használ.
   > 
   > 
5. Válassza ki a **hely** , amelyre szeretné létrehozni a fürtöt. Ha azt tervezi, hogy már feltöltötte a key vault meglévő tanúsítványt szeretne használni, a Key vault szerepel ugyanabban a régióban kell használnia. 

### <a name="2-cluster-configuration"></a>2. Fürtkonfiguráció
![Hozzon létre egy csomópont típusa][CreateNodeType]

Konfigurálja a fürt csomópontjait. Csomóponttípusok határozzák meg, a Virtuálisgép-méretek, a virtuális gépek számát és azok tulajdonságait. A fürt több csomóponttípussal rendelkezhet, de az elsődleges csomóponttípushoz (az első egy Ön által meghatározott a portálon) kell rendelkeznie legalább öt virtuális gépek esetében, mivel ez a csomópont típusa, a Service Fabric-rendszerszolgáltatások kerülnek. Ne konfiguráljon **elhelyezési tulajdonságokat** , mert a rendszer automatikusan hozzáadja egy alapértelmezett elhelyezési "NodeTypeName" tulajdonságát.

> [!NOTE]
> Egy általános forgatókönyv több csomópont esetében egy alkalmazást, amely tartalmazza az előtér-szolgáltatás és a egy háttér-szolgáltatás. Az előtér-szolgáltatást a kisebb méretű virtuális gépet (VM-méretek D2_V2 hasonlóan) a nyitott portokkal az internethez, és a háttérszolgáltatás elhelyezése nagyobb virtuális gépek (VM-méretek például D3_V2, D6_V2, D15_V2 és így tovább) portok megnyitása nélkül Internet felé néző szeretné.
> 

1. Válasszon egy nevet az adott csomóponttípus (csak betűket és számokat tartalmazó 1 és 12 karakter).
2. A minimális **mérete** virtuális gépek esetében az elsődleges csomópont típusa határozzák meg a **tartóssági szint** úgy dönt, hogy a fürt számára. A tartóssági szint alapértelmezés szerint bronz. A tartós további információkért lásd: [kiválasztása a Service Fabric-fürt tartóssági][service-fabric-cluster-durability].
3. Válassza ki a **virtuálisgép-méret**. D sorozatú virtuális gépek SSD meghajtók rendelkeznek, és különösen ajánlott azon állapotalapú alkalmazásokhoz. Ne használjon bármely részleges maggal rendelkező virtuális gép Termékváltozata vagy 10 GB-nál kisebb rendelkezésre álló szabad kapacitás rendelkezik. Tekintse meg [tervezési szempontok a dokumentum a service fabric-fürt] [ service-fabric-cluster-capacity] nyújt segítséget a virtuális gép méretének kiválasztásával.
4.  **Egyszeri csomópontot tartalmazó fürttel és három csomópontot tartalmazó fürt** csak tesztelési célokra szolgálnak. Ezek nem támogatottak az éles környezetben futó számítási feladatokat.
5. Válassza ki a **kezdeti Virtuálisgép-méretezési kapacitás** a csomóponttípus. Méretezhetők felfelé és lefelé a virtuális gépek száma a csomópont típusa később, de az elsődleges csomóponttípushoz, a legalább öt, a termelési számítási feladatokhoz. Más csomóponttípusok legalább egy virtuális gép lehet. A minimális **szám** virtuális gépek az elsődleges csomópont típusa meghajtók esetében a **megbízhatóság** a fürt.  
6. Konfigurálása **egyéni végpontok**. Ez a mező lehetővé teszi, hogy az Azure Load Balancerrel együttműködve az alkalmazások számára a nyilvános interneten keresztül elérhetővé tenni kívánt portok vesszővel elválasztott listáját adja meg. Például ha azt tervezi, a fürt webes alkalmazás üzembe helyezése, itt adhatja meg "80", hogy a forgalmat a 80-as portot a fürtbe. A végpontok további információkért lásd: [alkalmazásokkal való kommunikáció közben][service-fabric-connect-and-communicate-with-services]
7. **Fordított proxy engedélyezése**.  A [Service Fabric fordított proxyja](service-fabric-reverseproxy.md) segít mikroszolgáltatások Service Fabric-fürtön futó felderítése és kommunikálni más szolgáltatásokkal, amelyek http-végpontokat.
8. Térjen vissza a **fürtkonfiguráció** panel alatt **+ választható beállítások megjelenítése**, fürt konfigurálása **diagnosztikai**. Alapértelmezés szerint vannak engedélyezve a diagnosztika a fürtre, amelyek segítik a problémák elhárítása. Ha le szeretné tiltani a diagnosztikai módosítása a **állapot** kapcsolót **ki**. A diagnosztika kikapcsolása **nem** ajánlott. Ha már létrehozott Application Insights-projekttel rendelkezik, majd adjon annak a kulcsát, hogy az alkalmazások nyomkövetéseit legyenek irányítva.
9. **DNS szolgáltatással**.  A [DNS-szolgáltatás](service-fabric-dnsservice.md) egy opcionális szolgáltatás, amely lehetővé teszi, hogy más szolgáltatásokat, a DNS protokoll használatával.
10. Válassza ki a **Fabric frissítési módot** azt szeretné, hogy a fürt beállítása. Válassza ki **automatikus**, ha azt szeretné, hogy a rendszer automatikusan folytattuk a munkát az elérhető legújabb verzióra, és próbálja meg frissíteni a fürt hozzá. Állítsa a módot **manuális**, ha szeretne egy támogatott verzióját. A további részleteket a Fabric frissítési mód lásd: a [dokumentum Service Fabric-fürt frissítése.][service-fabric-cluster-upgrade]

> [!NOTE]
> Csak a Service Fabric támogatott verzióit futtató fürtöket is nyújtunk támogatást. Válassza a **manuális** módban készítésének felelősségére frissítse a fürtöt egy támogatott verziójára.
> 

### <a name="3-security"></a>3. Biztonság
![Képernyőfelvétel a biztonsági konfigurációk az Azure Portalon.][BasicSecurityConfigs]

Ahhoz, hogy egy biztonságos tesztfürt létrehozásáról egyszerű az Ön számára, adtunk a **alapszintű** lehetőséget. Ha már rendelkezik egy tanúsítvánnyal, és már feltöltött, hogy a [a key vault](/azure/key-vault/) (és engedélyezve van a key vault üzembe helyezéshez), majd használja a **egyéni** lehetőség

#### <a name="basic-option"></a>Alapszintű beállítás
Kövesse a képernyőn hozzáadása vagy újból felhasználhatja a meglévő kulcstároló, és adjon hozzá egy tanúsítványt. A tanúsítvány hozzáadása egy szinkron folyamat, és úgy kell várja meg a tanúsítványt létrehozni.

Ellenállni a kísértésnek, a képernyőre navigál, mindaddig, amíg az előző folyamat befejeződött.

![CreateKeyVault]

Most, hogy a key vault jön létre, a kulcstartó hozzáférési szabályzatainak szerkesztése 

![CreateKeyVault2]

Kattintson a a **hozzáférési házirend szerkesztése**, majd **speciális hozzáférési szabályzatok megjelenítése** , és engedélyezze a hozzáférést az Azure Virtual Machines üzembe helyezéshez. Javasoljuk, hogy engedélyezze-e, valamint a sablon üzembe helyezéséhez. Miután elvégezte a választott beállításokat, ne felejtse el kattintson a **mentése** gombra, és zárja be a a **hozzáférési házirendek** ablaktáblán.

![CreateKeyVault3]

Adja meg a tanúsítvány nevét, és kattintson a **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Egyéni beállítást
Ha már elvégezte a lépéseket, hagyja ki ebben a szakaszban a **alapszintű** lehetőséget.

![SecurityCustomOption]

Szüksége van a forrás key vaultnak, tanúsítvány URL-címe és tanúsítvány-ujjlenyomat-információit végezze el a lapon. Ha nincs kéznél legyen, nyissa meg egy másik böngészőablakban, és az Azure Portalon tegye a következőket

1. Keresse meg a key vault szolgáltatás.
2. Válassza a "Tulajdonságok" lapot, és másolja erőforrás-azonosító "A forrás key vaultnak" böngésző ablakban 

    ![CertInfo0]

3. Most válassza a "Tanúsítványok" lapot.
4. Kattintson a tanúsítvány ujjlenyomatát, amely végigvezeti a verziók lapra.
5. Kattintson a GUID megváltozott a jelenlegi verzió alatt látható.

    ![CertInfo1]

6. Az alábbi képernyőképen például most meg kell lennie. A hexadecimális ujjlenyomat SHA-1 "Tanúsítvány ujjlenyomata" (a böngésző ablakához másolása
7. Másolja a titkos kód azonosítója a "tanúsítvány URL-címe" a másik böngészőablakban.

    ![CertInfo2]

Ellenőrizze a **speciális beállítások konfigurálása** mezőben adja meg az ügyféltanúsítványok **rendszergazdai ügyfél** és **csak olvasható ügyfél**. Ezekben a mezőkben adja meg a rendszergazdai ügyféltanúsítvány ujjlenyomata és az olvasási jogosultsággal rendelkező felhasználó ügyféltanúsítvány ujjlenyomatát, ha van ilyen. A rendszergazdák próbál csatlakozni a fürthöz, amikor megkapják az itt megadott hozzáférés csak akkor, ha rendelkeznek, amely megfelel az ujjlenyomat-értékeket egy ujjlenyomattal rendelkező tanúsítvány.  

### <a name="4-summary"></a>4. Összegzés

Most már készen áll a fürt üzembe helyezéséhez. Mielőtt ezt megtenné, töltse le a tanúsítványt, keresse meg a hivatkozás a nagyméretű kék tájékoztató mezőben belül. Győződjön meg arról, hogy a tanúsítvány biztonságos helyen. szüksége lesz rá a fürthöz való csatlakozáshoz. Mivel a letöltött tanúsítvány nem rendelkezik a jelszót, javasolt egy hozzáadása.

A fürt létrehozásának befejezéséhez kattintson a **létrehozás**. A sablon igény szerint töltheti le.

![Összegzés]

A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a „Harang” ikonra az állapotsor mellett, a képernyő jobb felső részén.) Ha a fürt létrehozásakor **A kezdőpulton rögzít** lehetőségre kattintott, a **Service Fabric-fürt üzembe helyezése** a **Kezdőpultra** rögzítve látható. Ez a folyamat hosszabb időt vesz igénybe. 

A fürt Powershell vagy parancssori felület használatával a kezelési műveletek végrehajtásához, a fürthöz való csatlakozáshoz szüksége további útmutatást a [csatlakozni a fürthöz,](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>A fürt állapotának megtekintése
![Képernyőfelvétel a fürt részletes adatai az irányítópulton.][ClusterDashboard]

Miután a fürt létrejött, a fürt a portálon ellenőrizheti:

1. Lépjen a **Tallózás** kattintson **Service Fabric-fürtök**.
2. Keresse meg a fürthöz, és kattintson rá.
3. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

A **figyelő csomópont** szakasz a fürt irányítópultján panel azt jelzi, hogy a megfelelő és nem kifogástalan állapotú virtuális gépek száma. További információt a fürt állapota: annak [Service Fabric health-modell bemutatása][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric-fürtök bizonyos számú rendelkezésre állását, és állapot - nevezik "kvórum fenntartása" megőrzése mindig fel lehet csomópontokat igényelnek. Így már általában nem biztonságos, a fürt összes gép leállítása, kivéve, ha először hajtott végre egy [teljes biztonsági mentést az állapot][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli csatlakozás egy virtuálisgép-méretezési példányt, vagy egy fürt csomópontja
A NodeType mindegyike megadhatja a fürt egy virtuálisgép-méretezési beállítás első eredményezi. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy biztonságos fürthöz, a felügyeleti hitelesítéshez tanúsítványokat használ. Ezután [csatlakozni a fürthöz](service-fabric-connect-to-secure-cluster.md) , és ismerje meg, hogyan [titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md).  Emellett ismerje [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
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
[összegzés]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
