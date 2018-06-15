---
title: Azure App Service-környezet használata
description: Hogyan létrehozását, közzétételét és alkalmazásokat az Azure App Service-környezet skálázása
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 66ef20616df77dc809a79e516a53133a80759dc7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355304"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata #

## <a name="overview"></a>Áttekintés ##

Az Azure App Service Environment-környezet az Azure App Service egy alhálózatba Azure virtuális hálózatban egy ügyfél központi telepítését. Áll:

- **Előtérkiszolgáló-végpontok**: az előtér-webkiszolgálóinak, ahol a HTTP/HTTPS egy App Service-környezetben (ASE) leáll.
- **Feldolgozók**: A munkavállalók az erőforrásokat, amelyek az alkalmazások tárolására.
- **Adatbázis**: az adatbázis tartalmazza, amely meghatározza a környezetben.
- **Tárolási**: A tárolót használja a rendszer az ügyfél-közzétett alkalmazások futtatásához.

> [!NOTE]
> App Service Environment-környezet két verziója van: ASEv1 és ASEv2. ASEv1 az erőforrások kell kezelni, mielőtt használhatná azokat. Megtudhatja, hogyan konfigurálhatja és kezelheti a ASEv1, lásd: [konfigurálása egy App Service-környezet v1][ConfigureASEv1]. Ez a cikk többi ASEv2 összpontosít.
>
>

Telepíthet egy ASE (ASEv1 és ASEv2) egy külső vagy belső virtuális IP-címre az alkalmazás eléréséhez. A központi telepítés egy külső virtuális IP-címre gyakran nevezik egy külső ASE. A belső verzió a ILB ASE nevezik, mert egy belső terheléselosztón (ILB) használ. A ILB ASE kapcsolatos további információkért lásd: [létrehozása és használata egy ILB ASE][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Webalkalmazás létrehozása a Service-környezetben ##

Egy webalkalmazás létrehozása a Service-környezetben, azt ugyanazzal az eljárással, általában létrehozása, de néhány kisebb különbség az. Mikor hozzon létre egy új App Service-csomagot:

- Helyett válassza a földrajzi helyet, ahol az alkalmazás telepítéséhez, egy ASE az helyeként válassza.
- -Környezetben létrehozott összes App Service-csomagok IP-címek egy elszigetelt kell lennie.

Ha még nem rendelkezik egy ASE, utasításait követve létrehozhat [App Service-környezet létrehozása][MakeExternalASE].

A webalkalmazás létrehozása a Service-környezetben:

1. Válassza ki **hozzon létre egy erőforrást** > **Web + mobil** > **webalkalmazás**.

2. Adja meg a webalkalmazás nevét. Ha már ki az App Service-csomag Service-környezetben, a tartománynév, az alkalmazás a ASE tartomány nevét jeleníti meg.

    ![Webes alkalmazás nevének kiválasztása][1]

3. Válasszon egy előfizetést.

4. Adjon meg egy új erőforráscsoport nevét, vagy válasszon **meglévő** , és válassza ki a legördülő listából.

5. Válassza ki az operációs rendszert. 

    * Egy új, előzetes verziójú funkciók üzemeltető egy Linux app Service-környezetben, így javasoljuk, hogy nem kell hozzáadni Linux alkalmazások be egy ASE termelési számítási feladatokhoz folyamatban. 
    * A Linux-alkalmazás hozzáadása egy ASE be azt jelenti, hogy a ASE is csak előzetes módban működik. 

5. Válassza ki a ASE meglévő App Service-csomagot, vagy hozzon létre egy újat az alábbiak szerint:

    a. Válassza ki **új**.

    b. Adja meg az App Service-csomag nevét.

    c. Válassza ki a ASE a a **hely** legördülő listából. Egy Linux app Service-környezetben futtató csakis 6 régiókban pillanatnyilag: **USA nyugati régiója, USA keleti régiója, Nyugat-Európában, Észak-Európa, Kelet-Ausztrália, Délkelet-Ázsia.** 

    d. Válasszon egy **elszigetelt** tarifacsomagra vált. Válassza ki **válasszon**.

    e. Kattintson az **OK** gombra.
    
    ![Elkülönített tarifacsomagok][2]

    > [!NOTE]
    > Linux-webalkalmazások és a Windows web apps a azonos App Service-csomag nem lehet, de a azonos App Service Environment-környezetben lehet. 
    >

6. Kattintson a **Létrehozás** gombra.

## <a name="how-scale-works"></a>Hogyan méretezése működik ##

Az App Service-csomag minden App Service-alkalmazást futtat. A tároló modell környezetek App Service-csomagok tárolására, de App Service-csomagok tárolására az alkalmazások. Akkor az alkalmazások, az App Service-csomag vertikális, és így az alkalmazások méretezése a csomagot.

A ASEv2 akkor az App Service-csomag, a szükséges infrastruktúra automatikusan kerül. Nincs a skálázási műveletek késleltetés során az infrastruktúra kerül. A ASEv1 a szükséges infrastruktúra hozzá kell adnia hoz létre, vagy az App Service-csomag kiterjesztése. 

A több-bérlős App Service, a méretezés oka általában azonnali erőforrások készlete áll rendelkezésre annak támogatásához. -Környezetben nincs ilyen puffer, és szükség esetén erőforrásokat.

Service-környezetben akár 100 példányok méretezheti. 100 logikailemez egy egyetlen App Service-csomag összes lehetnek, vagy több App Service-csomagokról pontjain.

## <a name="ip-addresses"></a>IP-címek ##

Egy alkalmazás egy dedikált IP-címet lefoglalni az App Service képes a. Ez a funkció érhető el az IP-alapú SSL konfigurálása után a [egy meglévő egyéni SSL-tanúsítvány kötését az Azure-webalkalmazásokban][ConfigureSSL]. -Környezetben van azonban a figyelmet a jelentősebb kivételt. Az IP-alapú SSL-Példánynak környezetben használandó további IP-cím nem vehető fel.

ASEv1 meg kell kiosztani az IP-címek erőforrások előtt is használhatja. ASEv2 használhatja őket az alkalmazásból, mint hogy az a több-bérlős App Service. Nincs mindig egy tartalék cím ASEv2 legfeljebb 30 IP-címet. Minden alkalommal, amikor valamelyik használja, egy másik jelenik meg, hogy egy címet mindig könnyen hozzáférhető legyen. Gyors egymásutánban címek késleltetési idő legyen szükséges, egy másik IP-címet, amely megakadályozza a hozzáadása IP lefoglalni időpontot.

## <a name="front-end-scaling"></a>Előtér-skálázás ##

A ASEv2 amikor az App Service-csomagokról a horizontális munkavállalók automatikusan támogatja azokat. Minden ASE két előtér-webkiszolgálóinak hozza létre. Továbbá az első karakterrel végződik automatikusan minden 15 példányok egy előtér sebességgel kibővítési az App Service-csomagokról. Például ha 15, akkor rendelkezik három előtér-webkiszolgálóinak. Ha 30 példányokhoz méretezni, majd, hogy négy első befejeződik, és így tovább.

Ez a szám előtér-webkiszolgálóinak bőven elegendő a legtöbb forgatókönyvek kell lennie. Azonban ki lehet terjeszteni gyorsabban. Módosíthatja a arány minden öt példányok mindig egy első befejezéséhez. Nincs a arány módosítására járnak. További információkért lásd: [Azure App Service szolgáltatás díjszabása][Pricing].

Előtér-erőforrások a HTTP/HTTPS-végpont a ASE a rendszer. Az alapértelmezett előtér-konfigurációval előtér / memóriahasználatát érték következetesen körülbelül 60 %. Ügyfél munkaterheléseinek előtér nem futtatható. Méretezési illetően előtér kulcsfontosságú tényező a CPU, amelyek célja a elsősorban HTTPS-forgalmat.

## <a name="app-access"></a>Alkalmazás-hozzáférés ##

Service-környezetben külső az alkalmazások létrehozásakor használt tartomány eltér a több-bérlős App Service. Ez magában foglalja a ASE nevét. Egy külső ASE létrehozásával kapcsolatos további információkért lásd: [App Service-környezet létrehozása][MakeExternalASE]. A tartománynév-külső környezetben a következőképpen néz *.&lt; asename környezet&gt;. p.azurewebsites.net*. Például, ha a ASE nevű _külső-ase_ és nevű alkalmazás működteti _contoso_ abban a ASE, lépjen a következő URL-címek:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Az URL-cím contoso.scm.external ase.p.azurewebsites.net szolgál a Kudu konzol eléréséhez, vagy az alkalmazás-közzététel webes telepítése. A Kudu konzolon információkért lásd: [Kudu konzol az Azure App Service-][Kudu]. A Kudu konzol lehetővé teszi egy webes felhasználói felület hibakeresés, fájlok feltöltése, szerkesztheti, és még sok más.

-Példánynak környezetben határozhatja meg a tartomány a központi telepítéskor. Egy ILB ASE létrehozásával kapcsolatos további információkért lásd: [létrehozása és használata egy ILB ASE][MakeILBASE]. Ha a tartomány nevét adja meg _ilb-ase.info_, az alkalmazásokat, hogy ASE használja, hogy a tartomány létrehozása során. Az alkalmazás nevű _contoso_, az URL-címei:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Közzététel ##

Csakúgy, mint a több bérlős App Service-környezetben is közzéteheti a:

- A webes telepítése.
- FTP.
- Folyamatos integrációt.
- Adatértékmezők áthúzása a Kudu konzolon.
- Egy IDE, mint például a Visual Studio, az eclipse-ben vagy az IntelliJ IDEA.

Egy külső mértékéig e közzétételi beállítások viselkedése azonos. További információkért lásd: [telepítése az Azure App Service][AppDeploy]. 

A közzététel a fő különbség van egy ILB ASE tekintetében. Egy ILB mértékéig a közzétételi végpontok elérhetők minden csak a Példánynak. A Példánynak virtuális hálózatban ASE alhálózaton magánhálózati IP-címe van. Ha nincs a Példánynak a hálózati hozzáférést, olyan alkalmazások, hogy ASE nem tehető közzé. Leírtaknak megfelelően [létrehozása és használata egy ILB ASE][MakeILBASE], konfigurálnia kell DNS az alkalmazások a rendszerben. Az SCM végpontot, amely tartalmaz. Ha ezek még nincs megfelelően beállítva, nem tehető közzé. A IDEs is kell rendelkeznie a Példánynak a hálózati hozzáférést közvetlenül való közzétételhez.

Internet alapú CI rendszereknek, például a Githubon és a Visual Studio Team Services, egy ILB mértékéig nem működnek, mert a közzétételi végpont nem érhető el az Internet. Ehelyett egy lekérési modellt használó CI-rendszert kell használnia, ilyen például a Dropbox.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Az alkalmazás közzétételi profil és az alkalmazás portálpaneljéhez láthatja (a **áttekintése** > **Essentials** és is **tulajdonságok**). 

## <a name="pricing"></a>Díjszabás ##

Az árképzés SKU nevű **elszigetelt** csak ASEv2 való használatra készült. A Termékváltozat árképzési elszigetelt ASEv2 tárolt összes App Service-csomagokról találhatók. App Service-csomag elkülönített díjszabás régiónként eltérőek lehetnek. 

Az App Service-csomagokról ára mellett nincs ASE magát egy simán arány. Simán sebessége a ASE mérete nem változik, és egy alapértelmezett további 1 aránya skálázás ASE infrastruktúra fizet előtér minden 15 App Service-csomag példányok.  

Ha az alapértelmezett méretezési minden 15 App Service-csomag példányok 1 előtér mérték nem elég gyors, mely első vége hozzáadott vagy a bejárati-végpontok méretének aránya módosíthatja.  Ha módosítja a arány vagy méretét, kell fizetnie az előtér-mag, amely alapértelmezés szerint nem lesz hozzáadva.  

Például ha a 10-re méretarány, előtér megjelenik minden 10 példányok az App Service-csomagokról. Az egyszerű díj hozzá van rendelve egy előtér minden 15-példányok méretezési mértéke. 10 méretarány, a díj a 10 App Service-csomag példányok hozzáadott harmadik előtér fizetnie. Nem kell azt a után kell fizetnie, 15 példányok elérésekor, mert automatikusan hozzáadják.

Ha 2 mag az első-vége a méretet, de nem módosíthatja a arány majd kell fizetnie a felesleges magok.  Egy ASE 2 első részek, így még akkor kell fizetnie 2 extra mag 2 mag első célok mérete nagyobb, ha automatikus méretezési küszöbérték alatt hozza létre.

További információkért lásd: [Azure App Service szolgáltatás díjszabása][Pricing].

## <a name="delete-an-ase"></a>Egy ASE törlése ##

Egy ASE törlése: 

1. Használjon **törlése** tetején a **App Service Environment-környezet** panelen. 

2. Adja meg annak megerősítéséhez, hogy törli-e a ASE nevét. Ha töröl egy ASE, összes benne lévő tartalom is törli. 

    ![ASE törlése][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
