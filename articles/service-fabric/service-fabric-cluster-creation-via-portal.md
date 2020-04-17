---
title: Service Fabric-fürt létrehozása az Azure Portalon
description: Ismerje meg, hogyan állíthat be egy biztonságos Service Fabric-fürtöt az Azure-ban az Azure Portalon és az Azure Key Vault használatával.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: e0cd3d5e5a37720134a5bce596bba211b375f19d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458317"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Service Fabric-fürt létrehozása az Azure-ban az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ez egy részletes útmutató, amely végigvezeti a Service Fabric-fürt (Linux vagy Windows) azure-beli azure-beli azure-beli azure-beli beállításának lépésein az Azure Portalon. Ez az útmutató végigvezeti a következő lépéseken:

* Hozzon létre egy fürtöt az Azure-portálon keresztül.
* Hitelesítse a rendszergazdákat a tanúsítványok használatával.

> [!NOTE]
> A speciálisabb biztonsági beállítások, például az Azure Active Directoryval való felhasználói hitelesítés és az alkalmazásbiztonságtanúsítvány-beállítások [hozása az Azure Resource Manager használatával.][create-cluster-arm]
> 
> 

## <a name="cluster-security"></a>Fürtbiztonság 
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. A tanúsítványok Service Fabricban való használatával kapcsolatos további információkért lásd a [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security].

Ha ez az első alkalom, hogy létrehoz egy szolgáltatásháló-fürtöt, vagy egy fürtet telepít a tesztszámítási feladatokhoz, átugorhatja a következő szakaszt (**Fürt létrehozása az Azure Portalon**) , és a rendszer a tesztszámítási feladatokat futtató fürtökhöz szükséges tanúsítványokat hoz létre. Ha éles számítási feladatokhoz állít be fürtöt, folytassa az olvasást.

#### <a name="cluster-and-server-certificate-required"></a>Fürt- és kiszolgálótanúsítvány (kötelező)
Ez a tanúsítvány a fürt biztonságossá tétele és a fürthöz való jogosulatlan hozzáférés megakadályozása érdekében szükséges. Több szempontból is biztosítja a fürtbiztonságot:

* **Fürt hitelesítése:** A csomópont-csomópont kommunikáció hitelesítése a fürtösszevonáshoz. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek ezzel a tanúsítvánnyal igazolni tudják identitásukat.
* **Kiszolgáló hitelesítése:** Hitelesíti a fürtfelügyeleti végpontokat egy felügyeleti ügyfélszámára, így a felügyeleti ügyfél tudja, hogy a valódi fürthöz beszél. Ez a tanúsítvány tls-t is biztosít a HTTPS-felügyeleti API-hoz és a Service Fabric Explorer HTTPS-en keresztül.

E célok elérése érdekében a bizonyítványnak a következő követelményeknek kell megfelelnie:

* A tanúsítványnak titkos kulcsot kell tartalmaznia.
* A tanúsítványt kulcscseréhez kell létrehozni, amely személyes adatcserefájlba (.pfx) exportálható.
* A tanúsítvány **tulajdonosnevének meg kell egyeznie a** Service Fabric-fürt eléréséhez használt tartománnyal. Ez szükséges a Fürt HTTPS-kezelési végpontjaihoz és a Service Fabric Explorer t biztosít. A `.cloudapp.azure.com` tartomány hitelesítésszolgáltatójától nem szerezhet be TLS/SSL-tanúsítványt. Szerezzen be egy egyéni tartománynevet a fürthöz. Amikor tanúsítványt kér egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell egyeznie a fürthöz használt egyéni tartománynévvel.

#### <a name="client-authentication-certificates"></a>Ügyfélhitelesítési tanúsítványok
További ügyféltanúsítványok hitelesítik a rendszergazdákat a fürtkezelési feladatokhoz. A Service Fabric két hozzáférési szinttel rendelkezik: **rendszergazdai** és **írásvédett felhasználó.** Legalább egyetlen, a közigazgatási hozzáféréshez szükséges tanúsítványt kell használni. A további felhasználói szintű hozzáféréshez külön tanúsítványt kell biztosítani. A hozzáférési szerepkörökkel kapcsolatos további információkért tekintse meg [a Service Fabric-ügyfelek szerepköralapú hozzáférés-vezérlését.][service-fabric-cluster-security-roles]

A Service Fabric használata érdekében nem kell feltöltenie az ügyfélhitelesítési tanúsítványokat a Key Vaultba. Ezeket a tanúsítványokat csak a fürtkezelésre jogosult felhasználóknak kell megadni. 

> [!NOTE]
> Az Azure Active Directory az ajánlott módja az ügyfelek hitelesítésének a fürtkezelési műveletekhez. Az Azure Active Directory használatához létre kell [hoznia egy fürtöt az Azure Resource Manager használatával.][create-cluster-arm]
> 
> 

#### <a name="application-certificates-optional"></a>Kérelmezési tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítvány telepíthető a fürtre alkalmazásbiztonsági okokból. A fürt létrehozása előtt vegye figyelembe azokat az alkalmazásbiztonsági forgatókönyveket, amelyekhez a csomópontokra egy tanúsítványt kell telepíteni, például:

* Az alkalmazás konfigurációs értékeinek titkosítása és visszafejtése
* Adatok titkosítása csomópontok között a replikáció során 

Az alkalmazástanúsítványok nem konfigurálhatók, amikor [fürtöt hoz létre az Azure Portalon keresztül.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md) Az alkalmazástanúsítványok fürtbeállítási időpontban történő konfigurálásához létre kell [hoznia egy fürtöt az Azure Resource Manager használatával.][create-cluster-arm] A létrehozás után alkalmazástanúsítványokat is hozzáadhat a fürthöz.

## <a name="create-cluster-in-the-azure-portal"></a>Fürt létrehozása az Azure Portalon

Hozzon létre egy éles fürt, hogy megfeleljen az alkalmazás igényeinek magában foglalja a tervezés, hogy segítsen, hogy erősen ajánlott, hogy olvassa el és ismerje meg a [Service Fabric fürt tervezési szempontok][service-fabric-cluster-capacity] dokumentum. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>A Service Fabric fürterőforrás keresése

Jelentkezzen be az [Azure Portalra][azure-portal].
Új **erőforrássablon** hozzáadásához kattintson az Erőforrás létrehozása gombra. Keresse meg a Service Fabric fürtsablont a **Piactéren** a **Minden**csoportban.
Válassza a **Service Fabric Cluster elemet** a listából.

![Keressen meg a Service Fabric fürtsablont az Azure Portalon.][SearchforServiceFabricClusterTemplate]

Nyissa meg a **Service Fabric cluster** panelt, és kattintson a **Létrehozás gombra.**

A **Create Service Fabric fürtpanel** a következő négy lépésből áll:

### <a name="1-basics"></a>1. Alapok
![Képernyőkép: új erőforráscsoport létrehozása.][CreateRG]

Az alapok panelen meg kell adnia a fürt alapvető adatait.

1. Adja meg a fürt nevét.
2. Adja meg a virtuális gépek távoli asztalának **felhasználónevét** és **jelszavát.**
3. Győződjön meg arról, hogy válassza ki azt az **előfizetést,** amelyhez telepíteni szeretné a fürtöt, különösen akkor, ha több előfizetéssel rendelkezik.
4. Új **erőforráscsoport**létrehozása . A legjobb, ha ugyanazt a nevet adja meg, mint a fürt, mivel segít később megtalálni őket, különösen akkor, ha módosítani próbálja a központi telepítést, vagy törölni szeretné a fürtöt.
   
   > [!NOTE]
   > Bár dönthet úgy, hogy egy meglévő erőforráscsoportot használ, célszerű új erőforráscsoportot létrehozni. Ez megkönnyíti a fürtök és az általa használt összes erőforrás törlését.
   > 
   > 
5. Válassza ki azt a **helyet,** ahol létre szeretné hozni a fürtöt. Ha azt tervezi, hogy egy meglévő tanúsítványt, amely már feltöltött egy key vault, akkor ugyanazzal a régióval kell használnia, amelyben a key vault van. 

### <a name="2-cluster-configuration"></a>2. Fürt konfigurációja
![Csomóponttípus létrehozása][CreateNodeType]

Konfigurálja a fürtcsomópontokat. A csomóponttípusok határozzák meg a virtuális gép méretét, a virtuális gépek számát és azok tulajdonságait. A fürt egynél több csomóponttípussal is rendelkezhet, de az elsődleges csomóponttípusnak (az elsőnek, amelyet a portálon definiál) legalább öt virtuális gépnek kell lennie, mivel ez az a csomóponttípus, ahol a Service Fabric rendszerszolgáltatások kerülnek. Ne konfigurálja **az Elhelyezési tulajdonságokat,** mert a program automatikusan hozzáadja a "NodeTypeName" alapértelmezett elhelyezési tulajdonságát.

> [!NOTE]
> Több csomóponttípus gyakori forgatókönyve egy olyan alkalmazás, amely előtér-szolgáltatást és háttérszolgáltatást tartalmaz. Kisebb virtuális gépekre (például D2_V2-es virtuálisgépekre) szeretné helyezni az előtér-szolgáltatást, és az internetre nyitott portokkal szeretné a háttérszolgáltatást nagyobb virtuális gépekre (például D3_V2, D6_V2, D15_V2 és így tovább) internethez kapcsolódó portok nélkül.
> 

1. Válasszon nevet a csomóponttípusnak (1–12 karakter, amely csak betűket és számokat tartalmaz).
2. Az elsődleges csomóponttípus virtuális gépeinek minimális **méretét** a fürthöz kiválasztott **tartóssági szint** határozza meg. A tartóssági szint alapértelmezett beállítása bronz. A tartósságról további információt [a Service Fabric-fürt tartósságának kiválasztásáról][service-fabric-cluster-durability]talál.
3. Válassza ki a **virtuális gép méretét**. A D sorozatú virtuális gépek SSD-meghajtókkal rendelkeznek, és erősen ajánlottak az állapotalapú alkalmazásokhoz. Ne használjon olyan virtuálisgép-termékváltozatokat, amelyek részleges magokkal rendelkeznek, vagy 10 GB-nál kevesebb lemezkapacitással rendelkeznek. A virtuális gép méretének kiválasztásához tekintse meg a [szolgáltatásháló-fürt tervezési megfontolási dokumentumát.][service-fabric-cluster-capacity]
4.  **Az egycsomópontos fürt és a három csomópont-fürt** csak tesztelésre szolgál. Ezek nem támogatottak a futó éles számítási feladatok.
5. Válassza ki a **csomóponttípus kezdeti virtuálisgép-méretezési kapacitását.** A csomóponttípusban lévő virtuális gépek számát később fel- vagy leskálázhatja, de az elsődleges csomópont típusán a minimális öt az éles számítási feladatokhoz. Más csomóponttípusok rendelkezhetnek legalább egy virtuális gép. Az elsődleges csomóponttípus virtuális gépeinek minimális **száma** növeli a fürt **megbízhatóságát.**  
6. **Egyéni végpontok konfigurálása**. Ebben a mezőben adhatja meg azazure load balancer-en keresztül az alkalmazások nyilvános internetének elérhetőporttartalmazó portok vesszővel tagolt listáját. Ha például egy webalkalmazást kíván telepíteni a fürtre, írja be ide a "80" értéket, hogy a 80-as porton a forgalmat engedélyezze a fürtbe. A végpontokkal kapcsolatos további információkért lásd: [Kommunikáció az alkalmazásokkal][service-fabric-connect-and-communicate-with-services]
7. **Fordított proxy engedélyezése**.  A [Service Fabric fordított proxy](service-fabric-reverseproxy.md) segít a Service Fabric-fürtben futó mikroszolgáltatások nak a http-végpontokkal rendelkező más szolgáltatások felderítésében és kommunikálásában.
8. A **Fürt konfigurációs** paneljén a **+Választható beállítások megjelenítése**területen konfigurálja a **fürtdiagnosztikát.** Alapértelmezés szerint a fürtön engedélyezve vannak a diagnosztika a hibaelhárítási problémák megoldásához. Ha le szeretné tiltani a diagnosztikát, módosítsa az **Állapot** kapcsolót **Ki**állásra. A diagnosztika kikapcsolása **nem** ajánlott. Ha már létrehozott Application Insights-projektet, majd adja meg a kulcsát, hogy az alkalmazás nyomkövetései hozzá irányítva legyenek.
9. **Dns-szolgáltatással együtt**.  A [DNS-szolgáltatás](service-fabric-dnsservice.md) egy választható szolgáltatás, amely lehetővé teszi, hogy más szolgáltatásokat keressen a DNS protokoll használatával.
10. Válassza ki azt a **Fabric frissítési módot,** amelyhez a fürtöt be szeretné állítani. Válassza **az Automatikus**lehetőséget, ha azt szeretné, hogy a rendszer automatikusan vegye fel a legújabb elérhető verziót, és próbálja meg frissíteni a fürtöt. Ha támogatott verziót szeretne választani, állítsa a üzemmódot **Kézi**beállításra. A Fabric frissítési módtovábbi részletekért tekintse meg a [Service Fabric fürtfrissítési dokumentum.][service-fabric-cluster-upgrade]

> [!NOTE]
> Csak olyan fürtöket támogatunk, amelyek a Service Fabric támogatott verzióit futtatják. A **Kézi** mód kiválasztásával vállalja a fürt támogatott verzióra való frissítésének felelősségét.
> 

### <a name="3-security"></a>3. Biztonság
![Képernyőkép az Azure Portal biztonsági konfigurációiról.][BasicSecurityConfigs]

Annak érdekében, hogy egy biztonságos tesztfürt beállítása könnyen az Ön számára, már biztosított a **Basic** opciót. Ha már rendelkezik tanúsítvánnyal, és feltöltötte azt a [key vaultba](/azure/key-vault/) (és engedélyezte a key vault központi telepítését), akkor használja az **Egyéni** beállítást

#### <a name="basic-option"></a>Alapbeállítás
A képernyőkön meglévő kulcstartó hozzáadásához vagy újbóli használatához, valamint egy tanúsítvány hozzáadásához kövesse. A tanúsítvány hozzáadása egy szinkron folyamat, ezért meg kell várnia a tanúsítvány létrehozását.

Álljon ellen annak a kísértésnek, hogy elnavigáljon a képernyőről, amíg az előző folyamat be nem fejeződik.

![CreateKeyVault]

Most, hogy a key vault létrejött, szerkesztheti a hozzáférési szabályzatok a key vault. 

![CreateKeyVault2]

Kattintson a **hozzáférési szabályzatok szerkesztése ,** majd **a speciális hozzáférési szabályzatok megjelenítése,** és az Azure virtuális gépek hez való hozzáférés engedélyezése üzembe helyezéshez. Javasoljuk, hogy engedélyezze a sablon központi telepítését is. A beállítások után ne felejtse el a **Mentés** gombra kattintani, és zárja be az **Access házirendek** ablaktábláját.

![CreateKeyVault3]

Írja be a tanúsítvány nevét, majd kattintson az **OK gombra.**

![CreateKeyVault4]

#### <a name="custom-option"></a>Egyéni beállítás
Hagyja ki ezt a szakaszt, ha már végrehajtotta az **Alapszintű** beállítás lépéseit.

![SecurityCustomOption]

A biztonsági lap kitöltéséhez szüksége van a forráskulcstartóra, a tanúsítvány URL-címére és a tanúsítvány ujjlenyomatára. Ha nem rendelkezik vele, nyisson meg egy másik böngészőablakot, és az Azure Portalon tegye a következőket:

1. Nyissa meg a kulcstartó szolgáltatást.
2. Válassza a "Tulajdonságok" lapot, és másolja a "FORRÁSazonosító" a "Source key vault" a másik böngésző ablakban 

    ![CertInfo0]

3. Most válassza a "Tanúsítványok" lapot.
4. Kattintson a tanúsítvány ujjlenyomatára, amely a Verziók lapra vezet.
5. Kattintson az aktuális verzió alatt látható GUID-okra.

    ![CertInfo1]

6. Most a képernyőn kell lennie, mint alább. Másolja a hexadecimális SHA-1 ujjlenyomatot a másik böngészőablak "Tanúsítvány ujjlenyomata" rajzlapra
7. Másolja a "titkos azonosítót" a másik böngészőablak "Tanúsítvány URL-címére".

    ![CertInfo2]

A **Speciális beállítások konfigurálása** jelölőnégyzetet a **rendszergazdai** és **írásvédett ügyfél**ügyféltanúsítványainak megadásához jelölje be. Ezekben a mezőkben adja meg a rendszergazdai ügyféltanúsítvány ujjlenyomatát és adott esetben az írásvédett felhasználói ügyféltanúsítvány ujjlenyomatát. Amikor a rendszergazdák megpróbálnak csatlakozni a fürthöz, csak akkor kapnak hozzáférést, ha rendelkeznek az itt megadott ujjlenyomatértékekkel rendelkező tanúsítvánnyal.  

### <a name="4-summary"></a>4. Összefoglaló

Most már készen áll a fürt telepítésére. Mielőtt ezt megtenné, töltse le a tanúsítványt, nézze meg a nagy kék információs doboz a linket. Ügyeljen arra, hogy a tanúsítvány biztonságos helyen maradjon. szüksége van rá a fürthöz való csatlakozáshoz. Mivel a letöltött tanúsítvány nem rendelkezik jelszóval, javasoljuk, hogy adjon hozzá egyet.

A fürt létrehozásának befejezéséhez kattintson a **Létrehozás gombra.** A sablont tetszés szerint letöltheti.

![Összefoglalás]

A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a képernyő jobb felső részén lévő állapotsor melletti "Csengő" ikonra.) Ha a fürt létrehozása kor a **Rögzítés a Startboardhoz** elemre kattintott, a **Service Fabric Cluster üzembe helyezése** a Start táblára rögzítve jelenik **meg.** Ez a folyamat némi időt vesz igénybe. 

Ahhoz, hogy felügyeleti műveleteket hajtson végre a fürtön a Powershell vagy a CLI használatával, csatlakoznia kell a fürthöz, és többet kell tudnia [arról, hogyan csatlakozhat a fürthöz.](service-fabric-connect-to-secure-cluster.md)

## <a name="view-your-cluster-status"></a>A fürt állapotának megtekintése
![Képernyőkép a fürt részleteiről az irányítópulton.][ClusterDashboard]

A fürt létrehozása után megvizsgálhatja a fürtöt a portálon:

1. Nyissa meg a **Tallózás** gombot, és kattintson **a Service Fabric-fürtök**elemre.
2. Keresse meg a fürtöt, és kattintson rá.
3. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

A fürt irányítópultjának **Csomópontfigyelő** szakasza a kifogástalan és nem kifogástalan virtuális gépek számát jelzi. A fürt állapotáról további részleteket a [Service Fabric állapotmodell bevezetésével][service-fabric-health-introduction]talál.

> [!NOTE]
> A Service Fabric-fürtök megkövetelik, hogy egy bizonyos számú csomópont mindig a rendelkezésre állás fenntartása és állapotmegőrzése érdekében - a "kvórum fenntartása". Ezért általában nem biztonságos a fürt összes gépének leállítása, kivéve, ha először teljes biztonsági másolatot végzett [az állapotról.][service-fabric-reliable-services-backup-restore]
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli csatlakozás virtuálisgép-méretezési készlethez vagy fürtcsomóponthoz
A fürtben megadott nodetypes-ek mindegyike egy virtuálisgép-méretezési csoport beállítását eredményezi. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy biztonságos fürttel, amely tanúsítványokat használ a felügyeleti hitelesítéshez. Ezután [csatlakozzon a fürthöz,](service-fabric-connect-to-secure-cluster.md) és ismerje meg, hogyan [kezelheti az alkalmazástitok.](service-fabric-application-secret-management.md)  További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
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
[Összefoglalás]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
