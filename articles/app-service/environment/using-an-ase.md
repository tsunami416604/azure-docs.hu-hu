---
title: Használja az App Service Environment-környezet – Azure
description: Hogyan létrehozását, közzétételét és alkalmazások méretezése az Azure App Service-környezet
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
ms.custom: seodec18
ms.openlocfilehash: eca6f7996b05e58614c8f15067dacabb13730396
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274717"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata #

## <a name="overview"></a>Áttekintés ##

Az Azure App Service Environment-környezet üzembe helyezése az Azure App Service-ben az ügyfél az Azure virtual network egyik alhálózatában. Ez a következőkből áll:

- **Előtér-vége**: Az előtérrendszerek, ahol a HTTP/HTTPS leállítja az App Service environment (ASE).
- **Feldolgozók**: A feldolgozók olyan erőforrást, amely az alkalmazások üzemeltetéséhez.
- **Adatbázis**: Az adatbázis tartalmazza, amely meghatározza a környezetben.
- **Tárolási**: Az ügyfél által közzétett alkalmazások tárolására szolgál.

> [!NOTE]
> Nincsenek App Service Environment-környezet két verziója: Az ASEv1 és ASEv2. Az asev1-ben az erőforrásokat kell kezelni, mielőtt használhatná őket. Ismerje meg, hogyan konfigurálhatja és kezelheti az asev1-ben, lásd: [konfigurálása az App Service-környezet v1][ConfigureASEv1]. Ez a cikk további részének ASEv2 összpontosít.
>
>

Telepíthet egy ASE Környezethez (ASEv1 és ASEv2) és a egy külső vagy belső virtuális IP-CÍMEK az alkalmazás eléréséhez. A telepítéssel egy külső virtuális IP-cím a külső ASE környezetben gyakran nevezik. A belső verzió az ILB ASE nevezik, mert egy belső terheléselosztóval (ILB) használ. Az ILB ASE kapcsolatos további információkért lásd: [létrehozása és használata az ILB ASE][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Hozzon létre egy webalkalmazást az ASE környezetben ##

Hozhat létre egy webalkalmazást az ASE környezetben, azt ugyanazzal az eljárással, általában létrehozása, de néhány kisebb eltéréssel. Amikor egy új App Service-csomagot hoz létre:

- Egy földrajzi helyet, ahol az alkalmazás telepítése helyett, az ASE egy helyet válasszon.
- Minden App Service-csomagok egy ASE környezetben létrehozott egy elkülönített tarifacsomag kell lennie.

Ha nem rendelkezik egy ASE Környezethez, létrehozhat egyet a következő témakör utasításait követve [hozzon létre egy App Service Environment-környezet][MakeExternalASE].

Webalkalmazás létrehozása az ASE környezetben:

1. Válassza ki **erőforrás létrehozása** > **Web + mobil** > **webalkalmazás**.

2. Adja meg a webalkalmazás nevét. Ha az ASE App Service-csomag kijelölve, a tartomány nevét az alkalmazás az ASE tartomány nevét jeleníti meg.

    ![Webes alkalmazás nevének kiválasztása][1]

1. Válasszon egy előfizetést.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon **meglévő** , és válassza ki azt a legördülő listából.

1. Válassza ki az operációs rendszert. 

    * Új előzetes verziójú funkció, az ASE környezetben egy Linux-alkalmazás üzemeltetése, ezért javasoljuk, hogy meg nem adja hozzá a Linux-alkalmazások az ASE éles számítási feladatokhoz éppen futó. 
    * Egy Linux-alkalmazás hozzáadása az ASE az azt jelenti, hogy az ASE-t is az előnézeti módban. 

1. Válassza ki a meglévő App Service-csomagot az ASE környezetben, vagy hozzon létre egy újat az alábbi lépéseket:

    a. Válassza ki **létrehozása új**.

    b. Adja meg az App Service-csomag nevét.

    c. Válassza az ASE Környezetet a a **hely** legördülő listából. Az ASE környezetben egy Linux-alkalmazás üzemeltetése csak engedélyezett 6 régióban jelenleg: **USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA, Nyugat-Európa, Észak-Európa, Kelet-Ausztrália, Délkelet-Ázsia.** 

    d. Válasszon egy **elkülönített** tarifacsomag. Válassza ki **kiválasztása**.

    e. Kattintson az **OK** gombra.
    
    ![Izolált díjcsomagok][2]

    > [!NOTE]
    > Linuxos web apps és a Windows web Apps alkalmazások azonos App Service-csomagot nem lehet, de az azonos App Service-környezet is lehet. 
    >

1. Kattintson a **Létrehozás** gombra.

## <a name="how-scale-works"></a>Hogyan skálázhatja működik ##

Minden App Service-alkalmazás az App Service-csomag fut. A tároló modellje környezetek tartsa az App Service-csomagok, és App Service-csomagok az alkalmazások tárolására. Amikor egy alkalmazást, akkor az App Service-csomag méretezheti, valamint így minden alkalmazás ugyanabban a csomagban lévő.

Az ASEv2, amikor az App Service-csomag a szükséges infrastruktúra automatikusan hozzáadódik. Míg az infrastruktúra adnak, nincs késleltetés használatával méretezi a műveleteket. Az asev1-ben a szükséges infrastruktúra hozzá kell adnia hoz létre vagy horizontális felskálázása az App Service-csomag. 

A több-bérlős App Service-ben skálázás oka általában azonnali erőforrások olyan készletét támogatja, számára könnyen hozzáférhető. Az ASE környezetben nincs ilyen puffer, és szükség esetén az erőforrások kiosztásakor.

Az ASE környezetben méretezhető legfeljebb 100 példány. Ezekhez a példányokhoz 100 összes egy egyetlen App Service-csomag is lehet, vagy több App Service-csomagok elosztva.

## <a name="ip-addresses"></a>IP-címek ##

Az App Service képes egy alkalmazást egy dedikált IP-címet lefoglalni. Ez a képesség érhető el az IP-alapú SSL konfigurálása után leírtak szerint [meglévő egyéni SSL-tanúsítvány kötése az Azure web appshez][ConfigureSSL]. Az ASE környezetben, van azonban egy jelentős kivétel. Nem adhat hozzá további IP-címeket az IP-alapú SSL, az ILB ASE környezetben használható.

Az asev1-ben kell az IP-címek kiosztására erőforrásként, mielőtt használhatná őket. ASEv2 használhatja őket az alkalmazásból ugyanúgy, mint a több-bérlős App Service-ben. Az mindig van egy tartalék cím ASEv2 legfeljebb 30 IP-cím. Minden alkalommal, amikor valamelyik használja, egy másik egészül ki, hogy egy cím mindig könnyen elérhető. Egymás után gyorsan címek késleltetés szükség egy másik IP-címet, amely megakadályozza az IP-Címének hozzáadása lefoglalni időpontot.

## <a name="front-end-scaling"></a>Előtéri skálázással ##

Az ASEv2 horizontális felskálázás az App Service-csomagok feldolgozók automatikusan hozzáadódnak támogatja azokat. Két előtérrendszerek az ASE minden jön létre. Emellett az előtérrendszer automatikusan horizontális felskálázási gyakorisággal minden 15-példányok egy előtér az App Service-csomagok. Például ha 15 példánnyal is rendelkezik, akkor rendelkezik három előtérrendszerek. Ha 30-példányokhoz, akkor rendelkezik négy előtér-véget ér, és így tovább.

Ez a szám az előtérrendszerek bőven elegendő a legtöbb forgatókönyvek kell lennie. Azonban ki lehet terjeszteni gyorsabb ütemben. Módosíthatja a legalacsonyabb szinten egy első végéig minden öt példányban aránya. Nincs a arány módosítása esetén számítunk fel. További információkért lásd: [Azure App Service díjszabás][Pricing].

Előtérbeli erőforrásai az ASE-t a HTTP/HTTPS-végpontokat. Az alapértelmezett előtérbeli konfigurációhoz memória használata egy előtér következetesen mintegy 60 %-os. Ügyfél munkaterheléseinek a kezelőfelületen nem futnak. Tanúsítson tiszteletet méretezése egy előtér kulcsfontosságú tényező a CPU, amely elsősorban hajtja, HTTPS-forgalmat.

## <a name="app-access"></a>Alkalmazás-hozzáférés ##

A külső ASE környezetben az alkalmazások létrehozásakor használt tartomány eltér a több-bérlős App Service-ben. Az ASE nevét tartalmazza. Külső ASE létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy App Service Environment-környezet][MakeExternalASE]. A külső ASE tartománynév néz *.&lt; asename&gt;. p.azurewebsites.net*. Például, ha az ASE neve _külső-ase_ és a egy nevű alkalmazás üzemeltetése _contoso_ abban, hogy az ASE, hogy elérhetővé válik, a következő URL-címek:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Az URL-cím contoso.scm.external ase.p.azurewebsites.net szolgál a Kudu konzol eléréséhez, vagy az alkalmazások közzétételi webes használatával helyezze üzembe. A Kudu konzol kapcsolatos tudnivalókat lásd: [Kudu konzol az Azure App Service][Kudu]. A Kudu konzol lehetővé teszi a webes felhasználói felületen a hibakeresés, a fájlok feltöltése, szerkesztheti, és még sok más.

Az ILB ASE határozhatja meg a tartomány üzembe helyezéskor. Az ILB ASE létrehozásával kapcsolatos további információkért lásd: [létrehozása és használata az ILB ASE][MakeILBASE]. Ha a tartomány nevét adja meg _ilb-ase.info_, az alkalmazások az ASE tartomány használja alkalmazás létrehozása során. Az alkalmazás nevű _contoso_, az URL-címek:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Közzététel ##

Csakúgy, mint a több-bérlős App Service-ben, az ASE környezetben teheti közzé a:

- A webes telepítése.
- FTP.
- Folyamatos integráció.
- Oszlopmezők áthúzása a Kudu konzol.
- Az integrált fejlesztői Környezetig, például a Visual Studio, az Eclipse és IntelliJ IDEA.

A külső ASE környezetben ezek a közzétételi beállítások minden úgy viselkedik. További információkért lásd: [üzembe helyezése az Azure App Service][AppDeploy]. 

A fő közzétételi különbség garanciát az ILB ASE környezetben. ILB ASE környezetben a közzétételi végpontjai érhetők el az összes csak az ILB keresztül. Az ILB személyes IP-címet a virtuális hálózatban az ASE alhálózat be van kapcsolva. Nem kell hálózati hozzáféréssel az ILB ASE olyan alkalmazások nem tehető közzé. Amint [létrehozása és használata az ILB ASE][MakeILBASE], kell konfigurálnia a DNS a-alkalmazások a rendszerben. Az SCM-végpontot, amely tartalmazza. Ha ezek még nincs megfelelően definiálva, nem tehető közzé. Az ide-ket is kell rendelkeznie az ILB való hálózati hozzáférés közvetlenül történő közzétételhez.

Internetes alapú CI rendszerek, például a GitHub és az Azure DevOps, nem működnek az ILB ASE környezetben, mert a közzététel végpontja nem elérhető az internetről. Ehelyett egy lekérési modellt használó CI-rendszert kell használnia, ilyen például a Dropbox.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Az alkalmazás közzétételi profilján és az alkalmazás portálpanelén láthatja (a **áttekintése** > **Essentials** és a **tulajdonságok**). 

## <a name="pricing"></a>Díjszabás ##

A díjszabás Termékváltozat nevű **elkülönített** csak ASEv2 való használatra készült. Az elkülönített díjszabású Termékváltozatba ASEv2 lévő üzemeltetett összes App Service-csomagok vannak. Izolált App Service csomag díjszabása régiónként eltérő lehet. 

Az App Service-csomagok díjszabása, mellett nincs és átalánydíjas, az ASE magát. Nem változik az ASE méretével és a havidíja fedezi az ASE-infrastruktúra egy alapértelmezett száma 1 további skálázás alapján, átalánydíjjal történik minden 15 App Service-csomag példányok előtér.  

Ha minden 15 App Service-csomag példányok 1 előtér alapértelmezett méretezés mértéke nem elég gyors, módosíthatja a arány melyik előtér-kiszolgálókon vannak hozzáadva, vagy a kezelőfelületek méretét.  Ha módosítja a arány vagy méretét, kell fizetnie az előtér-magok, amely alapértelmezés szerint nem kellene felvenni.  

Például a 10 méretarány módosíthatja, ha egy előtér megjelenik minden 10 példányok az App Service-csomagok. A számított átalánydíj ismerteti egy méretezési csoport minden 15-példányok egy előtér aránya. 10 méretezési arányú kell fizetnie a díjat a harmadik előtér-alkalmazást az App Service-csomag 10 példányra lesz hozzáadva. Nem kell fizetnie, vagy ha egyenlege eléri 15 példányok, mert a rendszer automatikusan hozzáadja.

Ha a méretet, a kezelőfelületek, 2 mag, de nem módosíthatja a arány majd kell fizetnie a további Processzormagok száma.  Az ASE létrehozása 2 az előtér-kiszolgálókon, tehát még 2 további Processzormagok lenne fizet, ha növeli a mérete 2 core előtér-kiszolgálókon történő automatikus skálázási küszöbérték alá.

További információkért lásd: [Azure App Service díjszabás][Pricing].

## <a name="delete-an-ase"></a>Az ASE törlése ##

Az ASE törlése: 

1. Használat **törlése** felső részén a **App Service Environment-környezet** panelen. 

1. Adja meg annak ellenőrzéséhez, hogy szeretné-e törölni az ASE nevét. Törli az ASE, törölje az összes benne lévő tartalom is. 

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
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
