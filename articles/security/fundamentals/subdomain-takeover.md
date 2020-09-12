---
title: Altartományon belüli átvétel megakadályozása Azure DNS alias-rekordokkal és Azure App Service egyéni tartományának ellenőrzése
description: Ismerje meg, hogyan kerülheti el az altartományok átvételének gyakori súlyossági kockázatát
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: faa61dc351bebd3d2a85ad229036e5b9fba9256e
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514611"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>A DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása

Ez a cikk ismerteti az altartományok átvételének gyakori biztonsági kockázatát, valamint azokat a lépéseket, amelyekkel elháríthatja a megoldást.


## <a name="what-is-subdomain-takeover"></a>Mi az altartomány átvétele?

Az altartományok közötti átvételek gyakori, nagy súlyosságú fenyegetést jelentenek az olyan szervezetek számára, amelyek rendszeresen hoznak létre és törölnek sok erőforrást. Az altartományok átvétele akkor fordulhat elő, ha olyan [DNS-rekorddal](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) rendelkezik, amely egy kiépített Azure-erőforrásra mutat. Az ilyen DNS-rekordokat "lelógó DNS"-bejegyzéseknek is nevezzük. A CNAME rekordok különösen sebezhetők a fenyegetéssel szemben. Az altartományok átvétele lehetővé teszi, hogy a rosszindulatú szereplők átirányítsák a szervezet tartománya számára a kártékony tevékenységeket végző helyekre irányuló forgalmat.

Egy altartomány átvételének gyakori forgatókönyve:

1. **LÉTREHOZÁSA**

    1. Kiépít egy Azure-erőforrást a teljes tartománynevével (FQDN) `app-contogreat-dev-001.azurewebsites.net` .

    1. A DNS-zónában egy CNAME rekordot kell hozzárendelni azzal az altartománnyal, `greatapp.contoso.com` amely az Azure-erőforrásra irányítja a forgalmat.

1. **MEGSZÜNTETÉS**

    1. Az Azure-erőforrás megszűnik vagy törlődik, miután már nincs rá szükség. 
    
        Ezen a ponton a CNAME rekordot `greatapp.contoso.com` *should* el kell távolítani a DNS-zónából. Ha a CNAME rekord nem lett eltávolítva, akkor aktív tartományként van meghirdetve, de nem irányítja át a forgalmat egy aktív Azure-erőforráshoz. Ez a "lelógó" DNS-rekord definíciója.

    1. A lógó altartomány `greatapp.contoso.com` már sebezhető, és egy másik Azure-előfizetéshez rendelt erőforráshoz rendelhető hozzá.

1. **ÁTVÉTELE**

    1. A gyakran elérhető módszerek és eszközök használatával a fenyegetést jelentő színész felfedi a lógó altartományt.  

    1. A fenyegetést tartalmazó színész egy Azure-erőforrást is kiépít a korábban vezérelt erőforrással megegyező teljes tartománynévvel. Ebben a példában a `app-contogreat-dev-001.azurewebsites.net` .

    1. A rendszer most átirányítja az altartományba küldött forgalmat `myapp.contoso.com` a rosszindulatú Actor erőforrására, ahol a tartalmat szabályozzák.



![Altartomány átvétele egy kiépített webhelyről](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Az altartomány átvételének kockázatai

Ha egy DNS-rekord olyan erőforrásra mutat, amely nem érhető el, a rekordot el kell távolítani a DNS-zónából. Ha még nem törölte, a "lelógó DNS" rekord, amely lehetővé teszi az altartományok átvételét.

A kilógó DNS-bejegyzések révén a fenyegetést jelentő szereplők átvehetik a társított DNS-név irányítását egy rosszindulatú webhely vagy szolgáltatás üzemeltetéséhez. A szervezet altartományán található kártékony lapok és szolgáltatások a következőket okozhatják:

- **Az altartomány tartalmának elvesztése** – negatívan megnyomható, hogy a szervezet nem tudja védeni a tartalmát, valamint a márka sérülését és a megbízhatóság elvesztését.

- **Cookie-k betakarítása a gyanútlan látogatóktól** – gyakori a webalkalmazások számára, hogy a munkamenet-cookie-kat az altartományokra (*. contoso.com) tegyék elérhetővé, ezért bármely altartomány elérheti őket. A fenyegetésekkel rendelkező szereplők az altartományok átvételével hozhatnak létre valódi keresett oldalt, megszerezhetik a gyanútlan felhasználókat a látogatásuk és a cookie-k (akár biztonságos cookie-k) betakarítására is. Gyakori tévhit, hogy az SSL-tanúsítványok használata megvédi a webhelyét és a felhasználók cookie-jait egy átvételből. Egy fenyegetést tartalmazó színész azonban használhatja a eltérített altartományt, hogy érvényes SSL-tanúsítványt kapjon és fogadjon. Az érvényes SSL-tanúsítványok hozzáférést biztosítanak számukra a cookie-k védelméhez, és tovább növelhetik a kártékony webhely vélt legitimitását.

- **Adathalászat-kampányok** – az eredeti megjelenésű altartományok adathalászat-kampányokba is felhasználhatók. Ez a kártékony webhelyek és az olyan MX-rekordok esetében igaz, amelyek lehetővé tennék a veszélyforrások számára, hogy olyan e-maileket kapjanak, amelyek egy ismert biztonságos márka megbízható altartományára irányulnak.

- **További kockázatok** – előfordulhat, hogy a rosszindulatú webhelyek más klasszikus támadásokkal, például az XSS-vel, a CSRF, a CORS megkerüléssel és egyebekkel is kiterjeszthetők.



## <a name="identify-dangling-dns-entries"></a>A lógó DNS-bejegyzések azonosítása

A szervezeten belüli DNS-bejegyzések azonosításához használja a Microsoft GitHub által üzemeltetett PowerShell-eszközeit ["Get-DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains).

Ez az eszköz segíti az Azure-ügyfelek számára, hogy az előfizetések vagy bérlők számára létrehozott meglévő Azure-erőforráshoz tartozó CNAME-vel rendelkező tartományokat sorolja fel.

Ha a CNAME fájlok más DNS-szolgáltatásokban vannak, és az Azure-erőforrásokra mutatnak, adjon meg egy bemeneti fájlban lévő CNAME adatokat az eszközhöz.

Az eszköz támogatja az alábbi táblázatban felsorolt Azure-erőforrásokat. Az eszköz kinyeri vagy bemenetként veszi fel az összes bérlő CNAME értékét.


| Szolgáltatás                   | Típus                                        | FQDNproperty                               | Példa                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | Microsoft. Network/frontdoors                | Properties. cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | Microsoft. Storage/storageaccounts           | Properties. primaryEndpoints. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | Microsoft. CDN/profilok/végpontok            | Properties. állomásnév                        | `abc.azureedge.net`             |
| Nyilvános IP-címek       | Microsoft. Network/nyilvános IP         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | Microsoft. Network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Azure Container Instance  | Microsoft. containerinstance/containergroups | Properties. Ip_cím. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | Microsoft. apimanagement/szolgáltatás             | Properties. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Azure App Service         | Microsoft. Web/Sites                         | Properties. defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service – bővítőhely | Microsoft. Web/Sites/Slots                   | Properties. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Előfeltételek

Futtassa a lekérdezést olyan felhasználóként, aki rendelkezik a következőkkel:

- legalább olvasói szintű hozzáférés az Azure-előfizetésekhez
- olvasási hozzáférés az Azure Resource graphhoz

Ha Ön a szervezete bérlője globális rendszergazdája, emelje ki a fiókját, hogy hozzáférjen az összes szervezet előfizetéséhez a [jogosultságszint-emelési hozzáférés az összes Azure-előfizetéshez és a felügyeleti csoportokhoz](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)című témakör útmutatása alapján.


> [!TIP]
> Az Azure Resource Graph szabályozási és lapozási korlátokat tartalmaz, amelyeket érdemes figyelembe vennie, ha nagy Azure-környezettel rendelkezik. [További](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) információ a nagyméretű Azure Resource-adatkészletek használatáról. 
> 
> Az eszköz előfizetési kötegek használatával kerülheti el ezeket a korlátozásokat.

### <a name="run-the-script"></a>A szkript futtatása

A parancsfájlnak két verziója van, mindkettő ugyanazokat a bemeneti paramétereket adja meg, és hasonló kimenetet hoz létre:

|Script  |Tájékoztatás  |
|---------|---------|
|**Get-DanglingDnsRecordsPsCore.ps1**    |A Parallel mód csak a 7. PowerShell 7-es vagy újabb verziójában támogatott, máskülönben a soros üzemmódot fogja futtatni.|
|**Get-DanglingDnsRecordsPsDesktop.ps1** |Csak a 6-nál alacsonyabb PowerShell-asztal/-verzió esetén támogatott, mivel ez a parancsfájl [Windows-munkafolyamatot](https://docs.microsoft.com/dotnet/framework/windows-workflow-foundation/overview)használ.|

További információt és a PowerShell-szkriptek letöltését a GitHubról: https://aka.ms/DanglingDNSDomains .

## <a name="remediate-dangling-dns-entries"></a>A lógó DNS-bejegyzések szervizelése 

Tekintse át a DNS-zónákat, és azonosítsa azokat a CNAME-rekordokat, amelyek le vannak tiltva vagy átkerültek. Ha az altartományok lelógó vagy átvétele megtörtént, távolítsa el a sebezhető altartományokat, és csökkentse a kockázatokat a következő lépésekkel:

1. A DNS-zónából távolítsa el az összes olyan CNAME rekordot, amely az erőforrások teljes tartománynevére mutat, már nincs kiépítve.

1. Ahhoz, hogy a forgalom a vezérlő erőforrásaihoz legyen irányítva, további erőforrásokat kell kiépítenie a lógó altartományok CNAME rekordjaiban megadott teljes tartománynévvel.

1. Tekintse át az alkalmazás kódját az adott altartományokra való hivatkozáshoz, és frissítse a helytelen vagy elavult altartományokra mutató hivatkozásokat.

1. Vizsgálja meg, hogy történt-e kompromisszum, és tegye meg a szervezete incidens-válaszának eljárásait. A probléma kivizsgálásával kapcsolatos tippek és ajánlott eljárások alább találhatók.

    Ha az alkalmazás logikája olyan titkokat tartalmaz, mint például a OAuth hitelesítő adatok elküldése a lógó altartományba, vagy az adatvédelmi szempontból bizalmas adatokat a lelógó altartományoknak küldték el, előfordulhat, hogy az adatok harmadik fél számára elérhetővé válnak.

1. Ismerje meg, hogy a CNAME rekord miért nem lett eltávolítva a DNS-zónából az erőforrás megszüntetése után, és tegye meg a szükséges lépéseket annak biztosítására, hogy a DNS-rekordok megfelelően frissüljenek az Azure-erőforrások jövőbeli kiépítésekor.


## <a name="prevent-dangling-dns-entries"></a>Lelógó DNS-bejegyzések megakadályozása

Győződjön meg arról, hogy a szervezete olyan folyamatokat hozott létre, amelyekkel megelőzhető a DNS-bejegyzések bevezetése, és az eredményül kapott altartomány-átvételek a biztonsági program kulcsfontosságú részét

Néhány Azure-szolgáltatás olyan funkciókat kínál, amelyek a megelőző intézkedések létrehozásában nyújtanak segítséget. A probléma megelőzésének egyéb módszereit a szervezete ajánlott eljárásain vagy szabványos működési eljárásain keresztül kell létrehozni.


### <a name="use-azure-dns-alias-records"></a>Azure DNS alias-rekordok használata

A Azure DNS [alias-rekordjai](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) megakadályozhatja, hogy egy DNS-rekord életciklusát egy Azure-erőforrással összekapcsolja. Vegyünk például egy olyan DNS-rekordot, amely alias-rekordként van minősítve, hogy egy nyilvános IP-címre vagy egy Traffic Manager profilra mutasson. Ha törli a mögöttes erőforrásokat, a DNS-alias rekord üres halmaz lesz. A továbbiakban nem hivatkozik a törölt erőforrásra. Fontos megjegyezni, hogy az alias-rekordokkal védhető, hogy milyen korlátokat biztosít a védelem. A lista jelenleg a következőre korlátozódik:

- Azure Front Door
- Traffic Manager-profilok
- Azure Content Delivery Network-(CDN-) végpontok
- Nyilvános IP-címek

A korlátozott szolgáltatási ajánlatok ellenére az alias-rekordok használatát javasoljuk, hogy amikor csak lehetséges, használjon alias-rekordokat.

[További](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) információ a Azure DNS alias-rekordjainak képességeiről.



### <a name="use-azure-app-services-custom-domain-verification"></a>Az Azure App Service egyéni tartományának ellenőrzése

Azure App Service DNS-bejegyzéseinek létrehozásakor hozzon létre egy asuid. altartomány A tartomány-ellenőrzési AZONOSÍTÓval rendelkező TXT-rekord. Ha egy ilyen TXT-rekord létezik, egyetlen másik Azure-előfizetés sem tudja érvényesíteni az egyéni tartományt, amelyet átvesznek. 

Ezek a rekordok nem akadályozzák meg, hogy valaki hozza létre a Azure App Servicet ugyanazzal a névvel, mint a CNAME-bejegyzésben. Anélkül, hogy bizonyítani tudja a tartománynév tulajdonjogát, a veszélyforrások nem fogadhatnak forgalmat, és nem vezérelhetik a tartalmat.

[További](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) információ arról, hogyan képezhető le egy meglévő egyéni DNS-név a Azure app Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Folyamatok létrehozása és automatizálása a fenyegetés enyhítése érdekében

Ez gyakran a fejlesztők és az operatív csapatok számára a kitakarítási folyamatok futtatására, hogy elkerülje a DNS-fenyegetéseket. Az alábbi eljárások segítségével biztosíthatja, hogy a szervezet elkerülje a fenyegetést. 

- **Megelőzési eljárások létrehozása:**

    - Tájékoztassa az alkalmazás-fejlesztőket a címek átirányításához, amikor erőforrásokat törölnek.

    - A szolgáltatások leszerelése után a szükséges ellenőrzések listájában helyezze el a "DNS-bejegyzés eltávolítása" lehetőséget.

    - Helyezzen [törlési zárolásokat](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) minden olyan erőforrásra, amely egyéni DNS-bejegyzést tartalmaz. A törlési zárolás azt jelzi, hogy a leképezést el kell távolítani az erőforrás megszüntetése előtt. Az ehhez hasonló mértékek csak a belső oktatási programok együttes használata esetén működhetnek.

- **Felderítési eljárások létrehozása:**

    - Rendszeresen tekintse át a DNS-rekordokat, és győződjön meg arról, hogy az altartományok mind le vannak képezve az Azure-erőforrásokra:

        - Létezik – lekérdezheti a DNS-zónákat az Azure altartományokra mutató erőforrásokhoz, például *. azurewebsites.net vagy *. cloudapp.azure.com (lásd [a hivatkozási listát](azure-domains.md)).
        - Ön rendelkezik a saját DNS-altartományai által megcélzott összes erőforrással.

    - Az Azure-beli teljes tartománynevek (FQDN) végpontok és az alkalmazás tulajdonosai szolgáltatás-katalógus karbantartása. A szolgáltatás-katalógus létrehozásához futtassa az alábbi Azure Resource Graph lekérdezési parancsfájlt. Ez a szkript a teljes tartománynév-végponti információit, amelyekhez hozzáfér, és egy CSV-fájlba exportálja azokat. Ha rendelkezik hozzáféréssel a bérlő összes előfizetéséhez, a szkript az alábbi minta parancsfájlban látható összes előfizetést figyelembe veszi. Ha az eredményeket egy adott előfizetésre szeretné korlátozni, szerkessze a szkriptet az ábrán látható módon.


- **Szervizelési eljárások létrehozása:**
    - A DNS-bejegyzések beolvasásakor a csapatnak meg kell vizsgálnia, hogy történt-e kompromisszum.
    - Vizsgálja meg, hogy a rendszer miért nem irányította újra az erőforrást, amikor leszerelték.
    - Törölje a DNS-rekordot, ha már nincs használatban, vagy mutasson a szervezete tulajdonában lévő megfelelő Azure-erőforrásra (FQDN).
 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a kapcsolódó szolgáltatásokról és az Azure-beli szolgáltatásokkal szembeni védelemről, tekintse meg a következő lapokat.

- [Azure DNS támogatja az egyéni tartományokhoz tartozó alias-rekordok használatát](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [A tartomány-ellenőrzési azonosító használata egyéni tartományok hozzáadásakor Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Azure PowerShell használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
