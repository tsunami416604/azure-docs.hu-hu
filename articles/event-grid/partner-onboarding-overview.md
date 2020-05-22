---
title: Bevezető Azure Event Grid partnerként
description: Azure Event Grid partneri témakör beírása. Ismerje meg az erőforrás-modellt és a közzétételi folyamatot a partneri témakörökben.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758826"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Bevezető Azure Event Grid partnerként

Ez a cikk azt ismerteti, hogyan használható a Event Grid partner erőforrásainak magánjellegű használata, és hogyan válhat nyilvánosan elérhető partneri témakör típusa.

Nincs szükség különleges engedélyre a közzétételi eseményekhez Event Grid-partnerként társított Event Grid-erőforrástípusok használatának megkezdéséhez. Valójában a mai napon is felhasználhatja az események közzétételét a saját Azure-előfizetései számára, és tesztelheti az erőforrás-modellt, ha a partnernek kellene lennie.

## <a name="becoming-an-event-grid-partner"></a>Event Grid partnerré válás

Ha szeretné, hogy egy nyilvános Event Grid partner legyen, kezdje az [űrlap](https://aka.ms/gridpartnerform)kitöltésével, majd vegye fel a kapcsolatot a Event Grid csapatával a következő címen: [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>A partneri témák működése
A partneri témákban megtalálhatók a meglévő architektúrák, amelyeket a Event Grid már használ az Azure-erőforrások (például a Storage és a IoT Hub) eseményeinek közzétételéhez, és nyilvánosan elérhetővé teszi ezeket az eszközöket bárki számára. Ezek az eszközök alapértelmezés szerint csak a saját Azure-előfizetésére vonatkoznak. Az események nyilvánosan elérhetővé tételéhez töltse ki a fenti űrlapot, és [lépjen kapcsolatba a Event Grid csapatával](mailto:gridpartner@microsoft.com).

A partneri témakörök lehetővé teszik az események közzétételét Azure Event Grid a több-bérlős felhasználásra.

### <a name="onboarding-and-event-publishing-overview"></a>Bevezetési és esemény-közzététel áttekintése

#### <a name="partner-flow"></a>Partneri folyamat

1. Hozzon létre egy Azure-bérlőt, ha még nem rendelkezik ilyennel.
1. A CLI használatával hozzon létre egy új Event Grid `partnerRegistration` . Ez az erőforrás olyan adatokat tartalmaz, mint például a megjelenítendő név, a leírás, a beállítási URI és így tovább.

    ![Partneri témakör létrehozása](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Hozzon létre egyet vagy többet `partnerNamespaces` minden olyan régióban, amelyen közzé kívánja tenni az eseményeket. Ennek részeként Event Grid szolgáltatás kiépít egy közzétételi végpontot (például `https://contoso.westus-1.eventgrid.azure.net/api/events` ) és a hozzáférési kulcsokat.

    ![Partneri névtér létrehozása](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Adja meg, hogy az ügyfelek hogyan regisztrálhatnak a rendszeren, hogy a partneri témakört szeretnék regisztrálni.
1. Lépjen kapcsolatba a Event Grid csapatával, és tudassa velünk, hogy szeretné, ha a partneri témakör nyilvánosan megismerje.

#### <a name="customer-flow"></a>Felhasználói folyamat

1. Az ügyfél felkeresi az Azure Portal az Azure-előfizetés AZONOSÍTÓját és az erőforráscsoportot, amelyet a partneri témakörben hoztak létre.
1. Az ügyfél a rendszeren keresztül egy partneri témakört fog kérni. Válaszként létre fog hozni egy esemény-alagutat a partner névterében.
1. Event Grid létre fog hozni egy **függőben lévő** partneri témakört az ügyfél Azure-előfizetésében és erőforráscsoporthoz.

    ![Esemény-csatorna létrehozása](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Az ügyfél a Azure Portalon keresztül aktiválja a partner témakört. Az események mostantól a szolgáltatástól az ügyfél Azure-előfizetéséhez is tartozhatnak.

    ![Partneri témakör aktiválása](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Erőforrás-modell

Alább látható az erőforrás-modell a partneri témakörökhöz.

### <a name="partner-registrations"></a>Partneri regisztrációk
* Erőforrás`partnerRegistrations`
* Használja: partnerek
* Leírás: rögzíti az SaaS-partner globális metaadatait (például név, megjelenítendő név, leírás, beállítási URI).
    
    A partnerek regisztrációjának létrehozása/frissítése a partnereknek szóló önkiszolgáló művelet. Ez az önkiszolgáló képesség lehetővé teszi a partnerek számára a teljes végpontok közötti folyamat kiépítését és tesztelését.
    
    Az ügyfelek csak a Microsoft által jóváhagyott partnerRegistrations érhetők el.
* Hatókör: létrehozás a partner Azure-előfizetésében. Metaadatok láthatók az ügyfelek számára a nyilvánosság előtt.

### <a name="partner-namespaces"></a>Partneri névterek
* Erőforrás: partnerNamespaces
* Használja: partnerek
* Leírás: regionális erőforrást biztosít az ügyfelek eseményeinek közzétételéhez. Minden partner-névtér közzétételi végpontot és hitelesítési kulcsokat tartalmaz. A névtér azt is ismerteti, hogy a partner hogyan kér partneri témakört egy adott ügyfél számára, és felsorolja az aktív ügyfeleket.
* Hatókör: a partner előfizetésében él.

### <a name="event-channel"></a>Esemény-csatorna
* Erőforrás`partnerNamespaces/eventChannels`
* Használja: partnerek
* Leírás: az esemény-alagutak tükrözik az ügyfél partneri témakörét. Egy esemény-alagút létrehozásával és a metaadatokban az ügyfél Azure-előfizetésének és erőforráscsoportének megadásával jelezheti, hogy Event Grid, hogy az ügyfélhez hozzon létre egy partneri témakört. Event Grid egy ARM-hívást fog kiadni, amely létrehoz egy megfelelő partnerTopic az ügyfél előfizetésében. A partneri témakör "függő" állapotban lesz létrehozva. Az egyes eventTunnel és partnerTopic között van egy 1-1-kapcsolat.
* Hatókör: a partner előfizetésében él.

### <a name="partner-topics"></a>Partneri témakörök
* Erőforrás`partnerTopics`
* Használja: ügyfelek
* Leírás: a partneri témakörök a Event Grid egyéni témaköréhez és rendszertémaköréhez hasonlóak. Minden partneri témakör egy adott forráshoz (például `Contoso:myaccount` ) és egy adott partneri témakörhöz (például "contoso") van társítva. Az ügyfelek a partneri témakörben esemény-előfizetéseket hozhatnak létre az események különböző eseménykezelők számára történő átirányításához.

    Az ügyfelek nem tudják közvetlenül létrehozni ezt az erőforrást. A partneri témakör létrehozásának egyetlen módja az esemény-alagutakat létrehozó partneri művelet.
* Hatókör: az ügyfél előfizetésében él.

### <a name="partner-topic-types"></a>Partneri témák típusai
* Erőforrás`partnerTopicTypes`
* Használja: ügyfelek
* Leírás: a partneri témák a bérlői szintű erőforrástípusok, amelyek lehetővé teszik, hogy az ügyfelek felfedezzék a jóváhagyott partneri témakörök listáját. Az URL-cím a következőképpen fog megjelenni:https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Hatókör: globális

## <a name="publishing-events-to-event-grid"></a>Események közzététele Event Grid
Amikor létrehoz egy partnerNamespace egy Azure-régióban, egy regionális végpontot és a hozzá tartozó hitelesítési kulcsokat fog kapni. A névtérben lévő összes ügyfél-esemény bújtatása számára események kötegeit teheti közzé ehhez a végponthoz. Az esemény "forrás" mezője alapján Azure Event Grid minden eseményt leképez a kapcsolódó partneri témakör (ek) vel.

### <a name="event-schema-cloudevents-v10"></a>Event Schema: CloudEvents 1.0-s verzió
Események közzététele Azure Event Grid a CloudEvents 1,0 séma használatával. Event Grid támogatja a strukturált módot és a kötegelt üzemmódot is. A CloudEvents 1,0 az egyetlen támogatott esemény sémája a partneri névterekhez.

### <a name="example-flow"></a>Példa folyamatra

1.  A közzétételi szolgáltatás HTTP-BEJEGYZÉST végez a következőhöz: `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
2.  A kérelemben adjon meg egy AEG-sas-Key nevű fejlécet, amely a hitelesítés kulcsát tartalmazza. Ez a kulcs a partnerNamespace létrehozása során lett kiépítve. Egy érvényes fejléc-érték például az AEG-sas-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
3.  Állítsa be a Content-type fejlécet az "Application/cloudevents-batch + JSON;" értékre. charset = UTF-8 ".
4.  Hajtson végre egy HTTP-BEJEGYZÉST a fenti közzétételi URL-címen az adott régiónak megfelelő események kötegével. Például:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

A partnerNamespace-végpontra történő közzététel után választ kap. A válasz egy szabványos HTTP-válasz kódja. Néhány gyakori Válasz:

| Eredmény                             | Válasz              |
|------------------------------------|-----------------------|
| Sikeres                            | 200 OK                |
| Az esemény adatformátuma helytelen formátumú    | 400 hibás kérelem       |
| Érvénytelen hozzáférési kulcs                 | 401 jogosulatlan      |
| Helytelen végpont                 | 404 Nem található         |
| Tömb vagy esemény meghaladja a méretkorlátot | 413 túl nagy a hasznos adat |

## <a name="reference"></a>Referencia

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-sablon](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-sablon sémája](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API-k](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI-bővítmény](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK-k
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Ugrás](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Következő lépések
- [A partneri témakörök áttekintése](partner-topics-overview.md)
- [Partneri témakörök bevezetésének űrlapja](https://aka.ms/gridpartnerform)
- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
