---
title: Vészhelyreállítás egyéni témakörökhöz az Azure Event Gridben
description: Ez az oktatóanyag bemutatja, hogyan állíthatja be az eseményszervezési architektúrát, ha az Event Grid szolgáltatás nem megfelelő állapotúvá válik egy régióban.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 87f8f79e2cf125fa5735653153d8fcaa781f5200
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76511518"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Saját vész-helyreállítási rendszer létrehozása egyéni témakörökhöz az Event Gridben
A vészhelyreállítás az alkalmazás funkcióinak súlyos elvesztéséből való helyreállításra összpontosít. Ez az oktatóanyag bemutatja, hogyan állíthatja be az eseményszervezési architektúrát, ha az Event Grid szolgáltatás egy adott régióban nem megfelelő állapotúvá válik.

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy aktív-passzív feladatátvételi architektúrát az Egyéni témakörökhöz az Event Gridben. A feladatátvételt úgy érheti el, hogy két régióban tükrözi a témaköröket és az előfizetéseket, majd kezeli a feladatátvételt, ha egy témakör nem megfelelő állapotúvá válik. Az ebben az oktatóanyagban lévő architektúra minden új forgalmat meghiúsul. fontos, hogy tudatában legyenek annak, hogy ezzel a beállítással a már repülés közben lévő események nem lesznek helyreállítva, amíg a sérült régió újra kifogástalan állapotba nem kerül.

> [!NOTE]
> Az Event Grid most már támogatja az automatikus geokatasztrófa-helyreállítást (GeoDR). Továbbra is megvalósíthatja az ügyféloldali vész-helyreállítási logikát, ha nagyobb ellenőrzést szeretne a feladatátvételi folyamaton. Az automatikus GeoDR-ról további információt a [Kiszolgálóoldali geokatasztrófa-helyreállítás az Azure Event Gridben talál.](geo-disaster-recovery.md)

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A feladatátvételi konfiguráció teszteléséhez szüksége lesz egy végpontra az események fogadásához. A végpont nem része a feladatátvételi infrastruktúra, de az eseménykezelőként fog működni, hogy megkönnyítse a tesztelést.

A tesztelés egyszerűsítése érdekében telepítsen egy [előre elkészített webalkalmazást,](https://github.com/Azure-Samples/azure-event-grid-viewer) amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.
Ügyeljen arra, hogy ezt az URL-t úgy jegyezze fel, ahogy később szüksége lesz rá.

1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Elsődleges és másodlagos témakörök létrehozása

Először hozzon létre két Eseményrács-témakört. Ezek a témakörök elsődleges és másodlagos ként fognak működni. Alapértelmezés szerint az események az elsődleges témakörön keresztül fognak folyni. Ha van egy szolgáltatáskimaradás az elsődleges régióban, a másodlagos veszi át.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

1. Az Azure főmenüjének bal felső sarkában válassza az **Összes szolgáltatás** > az Event Grid > válassza az **Eseményrács** **témaköreinek lehetőséget.**

   ![Eseményrács témakörei menü](./media/custom-disaster-recovery/select-topics-menu.png)

    Jelölje ki az Eseményrács témakörei melletti csillagot, ha hozzá szeretné adni az erőforrásmenühöz, hogy a jövőben könnyebben hozzáférhessen.

1. Az Eseményrács témakörei menüben válassza a **+ADD** lehetőséget az elsődleges témakör létrehozásához.

   * Adjon a témakörnek logikai nevet, és adja hozzá utótagként az "-primary" utótagot, hogy könnyen nyomon követhető legyen.
   * A témakör régiója lesz az elsődleges régió.

     ![Event Grid Topic elsődleges párbeszéd létrehozása](./media/custom-disaster-recovery/create-primary-topic.png)

1. A témakör létrehozása után keresse meg, és másolja a **Témakör végpontot**. később szüksége lesz az URI-ra.

    ![Eseményrács elsődleges témaköre](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. A témakör hozzáférési kulcsának beszerezése, amelyre később is szüksége lesz. Kattintson az Erőforrás menü **Access billentyűire,** és másolja az 1-es kulcsot.

    ![Elsődleges témakörkulcs beszerezni](./media/custom-disaster-recovery/get-primary-access-key.png)

1. A Témakör panelen kattintson az **+Esemény-előfizetés** elemre, és hozzon létre egy előfizetést, amely összeköti az eseményfogadó webhelyét az oktatóanyaghoz.

   * Adjon az esemény-előfizetéslogikai nevet, és adja hozzá a "-primary" utótagot, hogy könnyen nyomon követhető legyen.
   * Válassza a Végpont típusú webhook lehetőséget.
   * Állítsa be a végpontot az eseményfogadó eseményURL-címére, amely a következőhöz hasonló:`https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Eseményrács elsődleges esemény-előfizetése](./media/custom-disaster-recovery/create-primary-es.png)

1. Ismételje meg ugyanazt a folyamatot a másodlagos témakör és előfizetés létrehozásához. Ezúttal cserélje le az "elsődleges" utótagot a "-másodlagos" utótagra a könnyebb nyomon követés érdekében. Végül győződjön meg arról, hogy egy másik Azure-régióban. Bár bárhová elhelyezheti, ajánlott az [Azure párosított régiók használata.](../best-practices-availability-paired-regions.md) A másodlagos témakör és az előfizetés elhelyezése egy másik régióban biztosítja, hogy az új események akkor is, ha az elsődleges régió leáll.

Önnek kellene most volna:

   * Egy eseményfogadó webhely tesztelésre.
   * Elsődleges témakör az elsődleges régióban.
   * Az elsődleges témakört az eseményfogadó webhelyéhez csatlakoztató elsődleges esemény-előfizetés.
   * Másodlagos témakör a másodlagos régióban.
   * Az elsődleges témakört az eseményfogadó webhelyéhez csatlakoztató másodlagos esemény-előfizetés.

## <a name="implement-client-side-failover"></a>Ügyféloldali feladatátvétel implementálása

Most, hogy rendelkezik egy regionálisan redundáns témakörpárral és előfizetésbeállítással, készen áll az ügyféloldali feladatátvétel megvalósítására. Számos módja van ennek elvégzésére, de minden feladatátvételi implementációk lesz egy közös funkció: ha az egyik témakör már nem kifogástalan, a forgalom átirányítja a másik témakörbe.

### <a name="basic-client-side-implementation"></a>Alapvető ügyféloldali megvalósítás

A következő mintakód egy egyszerű .NET-közzétevő, amely mindig először az elsődleges témakörben tesz közzé. Ha nem sikerül, akkor feladatátvételt a másodlagos témakörben. Mindkét esetben, azt is ellenőrzi az egészségügyi api a `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`másik téma csinál egy GET on . Egy kifogástalan állapotú témakör mindig **200 OK-mal** kell válaszolnia, ha a **/api/health** végponton get történik.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Próba

Most, hogy az összes összetevő a helyén van, tesztelheti a feladatátvételi implementációt. Futtassa a fenti mintát a Visual Studio kódjában vagy kedvenc környezetében. Cserélje le a következő négy értéket a témakörök végpontjaira és kulcsaira:

   * primaryTopic - az elsődleges témakör végpontja.
   * secondaryTopic - a másodlagos témakör végpontja.
   * primaryTopicKey - az elsődleges témakör kulcsa.
   * secondaryTopicKey - a másodlagos témakör kulcsa.

Próbálja meg futtatni az esemény közzétevője. A teszteseményeknek az eseményrács-megjelenítőben kell megjedkednie az alábbihoz hasonlóan.

![Eseményrács elsődleges esemény-előfizetése](./media/custom-disaster-recovery/event-grid-viewer.png)

Annak érdekében, hogy a feladatátvétel működik, módosíthatja néhány karaktert az elsődleges témakör kulcs, hogy ez már nem érvényes. Próbálja meg újra futtatni a közzétevőt. Az Eseményrács-megjelenítőben továbbra is új események jelennek meg, azonban amikor a konzolt nézi, látni fogja, hogy azok most a másodlagos témakörön keresztül jelennek meg.

### <a name="possible-extensions"></a>Lehetséges bővítmények

Számos módja van, hogy kiterjesszék ezt a mintát az Ön igényeinek megfelelően. Nagy mennyiségű forgatókönyvek esetén érdemes lehet rendszeresen ellenőrizni a témakör állapoti api függetlenül. Így, ha egy téma volt, hogy menjen le, akkor nem kell ellenőrizni, hogy minden egyes közzé. Ha tudja, hogy egy témakör nem kifogástalan, alapértelmezés szerint közzéteheti a másodlagos témakörben.

Hasonlóképpen érdemes lehet végrehajtani a feladat-visszavételi logika az adott igények alapján. Ha a legközelebbi adatközpontban való közzététel kritikus fontosságú a késés csökkentése érdekében, rendszeresidőközönként megvizsgálhatja a feladatátvételt megadó témakör állapotapiját. Ha ismét kifogástalan állapotú, tudni fogja, hogy biztonságos a feladat-visszavétel a közelebbi adatközpontba.

## <a name="next-steps"></a>További lépések

- Információ az [események http-végponton való fogadásáról](./receive-events.md)
- Fedezze fel, hogyan [irányíthat eseményeket hibrid kapcsolatokba](./custom-event-to-hybrid-connection.md)
- További információ a [vészhelyreállításról az Azure DNS és traffic manager használatával](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)
