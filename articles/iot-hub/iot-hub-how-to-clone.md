---
title: Azure IoT-központ klónozása
description: Azure IoT-központ klónozása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429151"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT-központ klónozása egy másik régióba

Ez a cikk az IoT Hub klónozásának módjait ismerteti, és néhány kérdést tartalmaz, amelyeket meg kell válaszolnia a kezdés előtt. Az ioT-elosztók klónozásának több oka is lehet:
 
* A vállalatot egyik régióból a másikba helyezi át, például Európából Észak-Amerikába (vagy fordítva), és azt szeretné, hogy erőforrásai és adatai földrajzilag közel legyenek az új helyhez, ezért át kell helyeznie a központot.

* Egy fejlesztői és éles környezethez hoz létre egy központot.

* A több hubos magas rendelkezésre állás egyéni implementációját szeretné végezni. További információkért tekintse meg a Hogyan érhető el a [régióközi HA szakasz az IoT Hub magas rendelkezésre állású és vész-helyreállítási.](iot-hub-ha-dr.md#achieve-cross-region-ha)

* Növelni szeretné a hubhoz konfigurált [partíciók](iot-hub-scaling.md#partitions) számát. Ez akkor van beállítva, amikor először hozza létre a hubot, és nem módosítható. A cikkben szereplő információk segítségével klónozhatja a hubot, és a klón létrehozásakor növelheti a partíciók számát.

A hub klónozásához rendszergazdai hozzáféréssel rendelkező előfizetésre van szükség. Az új hubot egy új erőforráscsoportba és régióba helyezheti, ugyanabban az előfizetésben, mint az eredeti hub, vagy akár egy új előfizetésben. Csak nem használhatja ugyanazt a nevet, mert a hub nevének globálisan egyedinek kell lennie.

> [!NOTE]
> Jelenleg nincs elérhető funkció az IoT hub automatikus klónozására. Ez elsősorban egy manuális folyamat, és így meglehetősen hibahajlamos. A hub klónozásának összetettsége közvetlenül arányos a hub összetettségével. Például egy IT hub üzenet-útválasztás nélküli klónozása meglehetősen egyszerű. Ha az üzenettovábbítást csak egy összetettségként adja hozzá, a hub klónozása legalább egy nagyságrenddel bonyolultabbá válik. Ha a végpontok útválasztásához használt erőforrásokat is áthelyezi, az egy másik magniture-sorrend, amely bonyolultabb. 

## <a name="things-to-consider"></a>Megfontolandó dolgok

Az IoT-központ klónozása előtt több dolgot is figyelembe kell venni.

* Győződjön meg arról, hogy az eredeti helyen elérhető összes funkció elérhető az új helyen is. Egyes szolgáltatások előzetes verzióban érhetők el, és nem minden funkció érhető el mindenhol.

* A klónozott verzió létrehozása és ellenőrzése előtt ne távolítsa el az eredeti erőforrásokat. Miután eltávolított egy hubot, az örökre eltűnt, és nincs mód a beállítások vagy adatok helyreállítására, hogy ellenőrizze a hub megfelelő replikálását.

* Számos erőforrás globálisan egyedi neveket igényel, ezért a klónozott verziókhoz különböző neveket kell használnia. Azt is meg kell használni egy másik nevet az erőforráscsoport, amelyhez a klónozott hub tartozik. 

* Az eredeti IoT-központ adatait nem telepíti át. Ez magában foglalja a telemetriai üzenetek, felhőből eszközre (C2D) parancsokat, és a feladattal kapcsolatos információkat, például az ütemezések és az előzmények. A metrikák és a naplózási eredmények szintén nem kerülnek áttelepítésre. 

* Az Azure Storage-ba átirányított adatok vagy üzenetek esetében az adatokat az eredeti tárfiókban hagyhatja, átviheti az adatokat egy új tárolófiókba az új régióban, vagy a régi adatokat a helyén hagyhatja, és új tárfiókot hozhat létre az új adatok új helyén. Az adatok Blob storage-ban való áthelyezéséről az [AzCopy – Első lépések](../storage/common/storage-use-azcopy-v10.md)című témakörben talál további információt.

* Az Event Hubs és a Service Bus témakörök és várólisták adatai nem telepíthetők át. Ez az időponthoz és az üzenetek feldolgozása után nem tárolható.

* Az áttelepítés leállását ütemezze. Az eszközök klónozása az új elosztóhoz időbe telik. Ha az Importálás/exportálás módszert használja, a benchmark tesztelés feltárta, hogy körülbelül két órát vehet igénybe 500 000 eszköz áthelyezése, és négy óra egy millió eszköz áthelyezése. 

* Az eszközöket az új hubra másolhatja az eszközök leállítása vagy módosítása nélkül. 

    * Ha az eszközöket eredetileg A DPS használatával kiépítésre használták ki, az újraépítésük frissíti az egyes eszközökben tárolt kapcsolati adatokat. 
    
    * Ellenkező esetben az importálási/exportálási módszert kell használnia az eszközök áthelyezéséhez, majd az eszközöket módosítani kell az új hub használatához. Beállíthatja például, hogy az eszköz felhasználja az IoT Hub állomásnevét az iker kívánt tulajdonságokból. Az eszköz veszi az IoT Hub állomás nevét, válassza le az eszközt a régi hub, és csatlakoztassa újra az újat.
    
* Frissítenie kell a használt tanúsítványokat, hogy azokat az új erőforrásokkal használhassa. Valószínűleg valahol egy DNS-táblában van definiálva a hub , frissítenie kell a DNS-adatokat.

## <a name="methodology"></a>Módszertan

Ez az általános módszer azt javasoljuk, hogy egy IoT hub áthelyezése az egyik régióból a másikba. Az üzenet-útválasztás esetén ez azt feltételezi, hogy az erőforrások nem kerülnek át az új régióba. További információt az [Üzenetútválasztás című szakaszban](#how-to-handle-message-routing)talál.

   1. Exportálja a központot és annak beállításait egy Erőforrás-kezelő sablonba. 
   
   1. Hajtsa végre a sablon szükséges módosításait, például frissítse a név és a klónozott hub helyének összes előfordulását. Az üzenet-útválasztási végpontokhoz használt sablon ban lévő erőforrások esetében frissítse az erőforrás sablonjában lévő kulcsot.
   
   1. Importálja a sablont egy új erőforráscsoportba az új helyen. Ez hozza létre a klónt.

   1. Hibakeresés szükség szerint. 
   
   1. Adjon hozzá mindent, ami nem lett exportálva a sablonba. 
   
       A fogyasztói csoportok például nem kerülnek exportálásra a sablonba. A fogyasztói csoportokat manuálisan kell hozzáadnia a sablonhoz, vagy a központ létrehozása után használnia kell az [Azure Portalt.](https://portal.azure.com) Van egy példa egy fogyasztói csoport hozzáadására egy sablonhoz a cikkben [Az Azure Resource Manager sablon használata az IoT Hub üzenetútválasztás konfigurálásához.](tutorial-routing-config-message-routing-rm-template.md)
       
   1. Másolja az eszközöket az eredeti hubról a klónra. Ezt az [IoT hubra regisztrált eszközök kezelése](#managing-the-devices-registered-to-the-iot-hub)című szakasz ismerteti.

## <a name="how-to-handle-message-routing"></a>Az üzenettovábbítás kezelése

Ha a hub [egyéni útválasztást](iot-hub-devguide-messages-read-custom.md)használ, a sablon exportálása a hubhoz tartalmazza az útválasztási konfigurációt, de nem tartalmazza magukat az erőforrásokat. El kell döntenie, hogy az útválasztási erőforrásokat az új helyre helyezi át, vagy a helyén hagyja őket, és továbbra is "ahogy van". 

Tegyük fel például, hogy van egy hubja az USA nyugati részén, amely üzeneteket küld egy tárfiókba (az USA nyugati részén is), és át szeretné helyezni a hubot az USA keleti részére. Áthelyezheti a központot, és továbbra is átirányíthatja az üzeneteket az USA nyugati részén lévő tárfiókba, vagy áthelyezheti a központot, és áthelyezheti a tárfiókot is. Előfordulhat, hogy egy másik régióban lévő üzenetek útválasztása és végponterőforrásai egy kis teljesítmény lejön.

Az üzenet-útválasztást használó elosztót könnyen áthelyezheti, ha az útválasztási végpontokhoz használt erőforrásokat sem. 

Ha a hub üzenettovábbítást használ, két lehetősége van. 

1. Helyezze át az útválasztási végpontokhoz használt erőforrásokat az új helyre.

    * Az új erőforrásokat saját maga kell létrehoznia manuálisan az [Azure Portalon](https://portal.azure.com) vagy az Erőforrás-kezelő sablonok használatával. 

    * Amikor az új helyen létrehozza őket, át kell neveznie az összes erőforrást, mivel azok globálisan egyedi nevekkel rendelkeznek. 
     
    * Az új központ létrehozása előtt frissítenie kell az erőforrásneveket és az erőforráskulcsokat az új hub sablonjában. Az erőforrásoknak jelen kell lenniük az új hub létrehozásakor.

1. Ne helyezze át az útválasztási végpontokhoz használt erőforrásokat. Használja őket "a helyén".

   * Abban a lépésben, ahol szerkeszti a sablont, az új központ létrehozása előtt be kell olvasnia az egyes útválasztási erőforrások kulcsait, és el kell helyeznie őket a sablonba. 

   * A hub továbbra is hivatkozik az eredeti útválasztási erőforrásokra, és a konfigurált módon irányítja az üzeneteket.

   * Lesz egy kis teljesítmény lecsapódás, mert a hub és az útválasztási végpont erőforrásai nem ugyanazon a helyen.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Felkészülés a hub másik régióba való áttelepítésére

Ez a szakasz a hub áttelepítésével kapcsolatos konkrét utasításokat tartalmaz.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Keresse meg az eredeti központot, és exportálja egy erőforrássablonba.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Nyissa meg az **Erőforráscsoportok lehetőséget,** és jelölje ki az áthelyezni kívánt központot tartalmazó erőforráscsoportot. Az **Erőforrások** és a hub így is megkeresheti. Válassza ki a hubot.

1. Válassza a **sablon exportálása** elemet a hub tulajdonságainak és beállításainak listájából. 

   ![Képernyőkép az IoT Hub sablonjának exportálására vonatkozó parancsról.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. A sablon letöltéséhez válassza a **Letöltés** lehetőséget. Mentse a fájlt olyan helyre, ahol újra megtalálhatja. 

   ![Képernyőkép az IoT Hub sablonjának letöltésére vonatkozó parancsról.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>A sablon megtekintése 

1. Nyissa meg a Letöltések mappát (vagy azt a mappát, amelyet a sablon exportálásakor használt), és keresse meg a zip fájlt. Nyissa meg a zip fájlt, és keresse meg a nevű `template.json`fájlt. Jelölje ki, majd a Sablon másolásához válassza a Ctrl+C billentyűkombinációt. Lépjen egy másik mappába, amely nem a zip fájlban van, és illessze be a fájlt (Ctrl+V). Most már szerkesztheti.
 
    A következő példa egy általános hub nélkül útválasztási konfiguráció. Ez egy S1 rétegű hub (1 egységgel) nevű **ContosoTestHub29358** régióban **westus.** Itt van az exportált sablon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>A sablon szerkesztése 

Néhány módosítást kell végrehajtania, mielőtt a sablonnal létrehozhatná az új központot az új régióban. A sablon szerkesztéséhez használja a [VS Code-ot](https://code.visualstudio.com) vagy a szövegszerkesztőt.

#### <a name="edit-the-hub-name-and-location"></a>A központ nevének és helyének szerkesztése

1. Távolítsa el a paraméterek szakasz tetején - ez sokkal egyszerűbb, hogy csak használja a hub nevét, mert nem lesz több paraméter. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Módosítsa a nevet úgy, hogy a tényleges (új) nevet használja ahelyett, hogy lehívná azt egy paraméterből (amelyet az előző lépésben távolított el). 

    Az új hub, használja az eredeti hub nevét, valamint a string *clone* az új nevet. Először tisztítsa meg a hub nevét és helyét.
    
    Régi verzió:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Új verzió: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Ezután azt fogja találni, hogy az **elérési út** értékei tartalmazzák a régi hub nevet. Változtassa meg őket, hogy az újat használják. Ezek az **eventHubEndpoints** nevű **események** és **OperationsMonitoringEvents**elérési útértékei.

    Ha elkészült, az eseményközpont végpontjai szakasz a következőképpen kell kinéznie:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>A nem áthelyezett útválasztási erőforrások kulcsainak frissítése

Ha az útválasztással konfigurált elosztó erőforrás-kezelő sablonját exportálja, látni fogja, hogy az erőforrások kulcsai nem szerepelnek az exportált sablonban – elhelyezésüket csillagjelöli. Az új hub sablonjának importálása és a hub létrehozása **előtt** ki kell töltenie ezeket az erőforrásokat a portálon, és be kell töltenie azokat. 

1. Az útválasztási erőforrásokhoz szükséges kulcsok beolvasása és a sablonba való betöltésük. A kulcs(oka)t az Azure [Portalon](https://portal.azure.com)lekérheti az erőforrásból. 

   Ha például üzeneteket küld egy tárolóba, keresse meg a tárfiókot a portálon. A Beállítások csoportban válassza az **Access billentyűk**lehetőséget, majd másolja az egyik billentyűt. Így néz ki a kulcs a sablon első exportálásakor:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Miután lekérte a tárfiók fiókkulcsát, helyezze azt `AccountKey=****` a sablonba a záradékban a csillagok helyén. 

1. A szolgáltatásbusz-várólisták esetén a SharedAccessKeyName-nek megfelelő megosztott hozzáférési kulcsot kapjuk meg. Itt van a `SharedAccessKeyName` kulcs és a json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Ugyanez vonatkozik a Service Bus-témakörök és az Event Hub-kapcsolatok.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Az új útválasztási erőforrások létrehozása az új helyen

Ez a szakasz csak akkor érvényes, ha a hub által az útválasztási végpontokhoz használt erőforrásokat helyezi át.

Ha át szeretné helyezni a műveletterv-erőforrásokat, manuálisan kell beállítania az erőforrásokat az új helyen. Létrehozhatja az útválasztási erőforrásokat az [Azure Portalon](https://portal.azure.com)keresztül, vagy exportálhatja az Erőforrás-kezelő sablont az üzenet-útválasztás által használt erőforrásokhoz, szerkesztheti és importálhatja azokat. Az erőforrások beállítása után importálhatja a hub sablonját (amely tartalmazza az útválasztási konfigurációt).

1. Hozza létre a műveletterv által használt erőforrásokat. Ezt manuálisan is megteheti az [Azure Portalhasználatával,](https://portal.azure.com)vagy létrehozhatja az erőforrásokat az Erőforrás-kezelő sablonok használatával. Ha sablonokat szeretne használni, a következő lépéseket kell végrehajtania:

    1. A műveletterv által használt minden egyes erőforrásesetében exportálja azt egy Erőforrás-kezelő sablonba.
    
    1. Frissítse az erőforrás nevét és helyét. 

    1. Frissítse az erőforrások közötti kereszthivatkozásokat. Ha például létrehoz egy sablont egy új tárfiókhoz, frissítenie kell a tárfiók nevét a sablonban és minden más sablont, amely hivatkozik rá. A legtöbb esetben a hub sablonjában lévő útválasztási szakasz az egyetlen olyan sablon, amely hivatkozik az erőforrásra. 

    1. Importálja az egyes sablonokat, amelyek az egyes erőforrásokat telepítik.

    Miután a műveletterv által használt erőforrások bevannak állítva és futnak, folytathatja.

1. Az IoT hub sablonjában módosítsa az egyes útválasztási erőforrások nevét az új nevére, és szükség esetén frissítse a helyet. 

Most már rendelkezik egy sablont, amely létrehoz egy új hub, amely úgy néz ki, majdnem pontosan úgy néz ki, mint a régi hub, attól függően, hogy hogyan úgy döntött, hogy kezelje az útválasztást.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Áthelyezés - az új központ létrehozása az új régióban a sablon betöltésével

Hozza létre az új központot az új helyen a sablon használatával. Ha áthelyezni kívánt útválasztási erőforrásokkal rendelkezik, az erőforrásokat az új helyen kell beállítani, és a sablonban lévő hivatkozásokat az egyezéshez kell frissíteni. Ha nem helyezi át az útválasztási erőforrásokat, azoknak a sablonban kell lenniük a frissített kulcsokkal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** lehetőséget. 

1. A keresőmezőbe helyezze a "sablon központi telepítését", és válassza az Enter lehetőséget.

1. Válassza ki **a sablon központi telepítését (üzembe helyezés egyéni sablonokkal)**. Ezzel a sablon központi telepítésének képernyőjére kerül. Kattintson a **Létrehozás** gombra. Ekkor az alábbi képernyő jelenik meg:

   ![Képernyőkép a saját sablon felépítésének parancsával](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Válassza a Saját sablon létrehozása lehetőséget **a szerkesztőben,** amely lehetővé teszi a sablon fájlból való feltöltését. 

1. Válassza **a Fájl betöltése**lehetőséget. 

   ![Sablonfájl feltöltésének parancsát bemutató képernyőkép](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Tallózással keresse meg a szerkesztett új sablont, és jelölje ki, majd válassza a **Megnyitás**gombot. Betölti a sablont a szerkesztési ablakban. Kattintson a **Mentés** gombra. 

   ![Képernyőkép a sablon betöltését ábrázoló képernyőkép](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Töltse ki az alábbi mezőket.

   **Előfizetés**: válassza ki a használni kívánt előfizetést.

   **Erőforráscsoport**: új erőforráscsoport létrehozása új helyen. Ha már van egy új beállítása, akkor új létrehozása helyett kijelölheti azt.

   **Hely:** Ha egy meglévő erőforráscsoportot választott ki, akkor ez lesz kitöltve, hogy megfeleljen az erőforráscsoport helyének. Ha új erőforráscsoportot hozott létre, ez lesz a helye.

   **Egyetértek jelölőnégyzet**: ez alapvetően azt mondja, hogy ön vállalja, hogy fizetni az erőforrás (ok) te létre.

1. Válassza a **Beszerzés** gombot.

A portál most érvényesíti a sablont, és telepíti a klónozott hub. Ha rendelkezik útválasztási konfigurációs adatokkal, az az új hubban fog szerepelni, de az erőforrásokra fog mutatni az előző helyen.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Az IoT hubra regisztrált eszközök kezelése

Most, hogy már a klón, és fut, meg kell másolni az összes eszközt az eredeti hub a klón. 

Ennek többmódja is van. Vagy eredetileg [az eszközkiépítési szolgáltatást (DPS)](/azure/iot-dps/about-iot-dps)használta az eszközök kiépítéséhez, vagy nem. Ha igen, ez nem nehéz. Ha nem, ez nagyon bonyolult lehet. 

Ha nem a DPS használatával létesített eszközöket, kihagyhatja a következő szakaszt, és az [Importálás/exportálás parancászóval áthelyezheti az eszközöket az új elosztóra.](#using-import-export-to-move-the-devices-to-the-new-hub)

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>A DPS használata az új hubban lévő eszközök újbóli kiépítéséhez

Ha a DPS segítségével szeretné áthelyezni az eszközöket az új helyre, olvassa el az Eszközök újbóli kiépítése című [témakört.](../iot-dps/how-to-reprovision.md) Ha elkészült, megtekintheti az eszközöket az [Azure Portalon,](https://portal.azure.com) és ellenőrizheti, hogy az új helyen vannak-e.

Nyissa meg az új központot az [Azure Portal](https://portal.azure.com)használatával. Válassza ki a hubot, majd az **IoT-eszközök**lehetőséget. Láthatja az eszközöket, amelyek et újra kiépített a klónozott hub. A klónozott hub tulajdonságait is megtekintheti. 

Ha már megvalósított a művelettervezést, ellenőrizze, hogy az üzenetek megfelelően vannak-e átirányítva az erőforrásokhoz.

### <a name="committing-the-changes-after-using-dps"></a>A módosítások véglegesítése a DPS használata után

Ezt a módosítást a DPS szolgáltatás követte el.

### <a name="rolling-back-the-changes-after-using-dps"></a>A dps használata után a módosítások visszaállítása. 

Ha vissza szeretné vonni a módosításokat, állítsa ki újra az eszközöket az új hubról a régire.

Most már befejezte a hub és az eszközök áttelepítését. Akkor ugorjon a [Clean-up](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Az eszközök áthelyezése az új hubra az Importálás exportálásával

Az alkalmazás a .NET Core-t célozza meg, így Windows vagy Linux rendszeren futtatható. Letöltheti a mintát, lekérheti a kapcsolati karakterláncokat, beállíthatja azokat a jelzőket, amelyekbitjeit futtatni szeretné, és futtathatja azt. Meg tudod csinálni anélkül, hogy valaha is megnyitja a kódot.

### <a name="downloading-the-sample"></a>A minta letöltése

1. Használja az IoT C# mintákat ezen az oldalon: [Azure IoT-minták C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Töltse le a zip fájlt, és csomagolja ki a számítógépre. 

1. A vonatkozó kód ./iot-hub/Samples/service/ImportExportDevicesSample. Az alkalmazás futtatásához nem kell megtekintenie vagy szerkesztenie a kódot.

1. Az alkalmazás futtatásához adjon meg három kapcsolati karakterláncot és öt beállítást. Ezeket az adatokat parancssori argumentumként adja át, vagy környezeti változókat használ, vagy a kettő kombinációját használja. A beállításokat parancssori argumentumként, a kapcsolati karakterláncokat pedig környezeti változókként adjuk át. 

   Ennek az az oka, hogy a kapcsolati karakterláncok hosszúak és unainly, és nem valószínű, hogy változik, de érdemes lehet módosítani a beállításokat, és futtassa az alkalmazást többször. Egy környezeti változó értékének módosításához be kell zárnia a parancsablakot és a Visual Studio vagy a VS-kód értékét, attól függően, hogy melyiket használja. 

### <a name="options"></a>Beállítások

Az alkalmazás futtatásakor megadott öt beállítás a következőket tartalmazza. Egy perc múlva a parancssorba tesszük.

*   **addDevices** (argument 1) -- állítsa ezt igaz, ha azt szeretné, hogy adjunk virtuális eszközök, amelyek az Ön számára létrehozott. Ezek hozzáadódnak a forrásközponthoz. Is, meg **numToAdd** (argumentum 2), hogy adja meg, hogy hány eszközt szeretne hozzáadni. A hubra regisztrálható eszközök maximális száma egymillió. Ennek a beállításnak a célja a tesztelés – létrehozhat egy adott számú eszközt, majd átmásolhatja őket egy másik hubra.

*   **copyDevices** (argumentum 3) -- állítsa be ezt igaz az eszközök másolásához az egyik hubról a másikra. 

*   **deleteSourceDevices** (argument 4 argumentum) -- állítsa ezt igaz értékre a forrásközpontba regisztrált összes eszköz törléséhez. Javasoljuk, hogy várjon, amíg biztos lehet benne, hogy az összes eszköz átvitelre került, mielőtt futtatná ezt. Miután törölte az eszközöket, nem tudja visszakapni őket.

*   **deleteDestDevices** (5. argumentum) -- állítsa ezt igaz értékre a célközponthoz (a klónhoz) regisztrált összes eszköz törléséhez. Ezt akkor érdemes megtennie, ha többször szeretné másolni az eszközöket. 

Az alapparancs *a dotnet futtatása* lesz - ez azt mondja a .NET-nek, hogy építse fel a helyi csproj fájlt, majd futtassa azt. A parancssori argumentumokat a futtatás előtt hozzáadja a végponthoz. 

A parancssor a következő példákhoz fog hasonlítani:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Környezeti változók használata a kapcsolati karakterláncokhoz

1. A minta futtatásához a régi és az új IoT-központok hoz, valamint egy tárfiókhoz kell használnia az ideiglenes munkafájlokhoz. Ezek értékeit környezeti változókban tároljuk.

1. A kapcsolati karakterlánc-értékek leválasztásához jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

1. Helyezze a kapcsolati karakterláncokat olyan helyre, ahol lekérheti őket, például a NotePad-ot. Ha a következőt másolja, a kapcsolati karakterláncokat közvetlenül oda illesztheti be, ahová mennek. Ne adjon szóközt az egyenlőségjel köré, különben megváltoztatja a változó nevét. A kapcsolati karakterláncok körül nem kell dupla idézőjeleket végezni. Ha idézőjeleket helyez el a tárfiók kapcsolati karakterlánca körül, az nem fog működni.

   A Windows rendszerben a következő képpen állíthatja be a környezeti változókat:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux esetén így határozhatja meg a környezeti változókat:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Az IoT hub kapcsolati karakterláncok, a portál minden egyes hub. Az **Erőforrások** területen kereshet a hubot. Ha ismeri az erőforráscsoportot, nyissa meg az **Erőforráscsoportok**at , válassza ki az erőforráscsoportot, majd válassza ki a központot az adott erőforráscsoport eszközeinek listájából. 

1. Válassza **a megosztott hozzáférési szabályzatokat** a hub beállításai között, majd válassza az **iothubowner** lehetőséget, és másolja az egyik kapcsolati karakterláncot. Tegye ugyanezt a célközpontesetében is. Adja hozzá őket a megfelelő SET parancsokhoz.

1. A tárfiók kapcsolati karakterláncához keresse meg a tárfiókot az **Erőforrások** vagy az **Erőforrás csoport** ban, és nyissa meg. 
   
1. A Beállítások csoportban válassza az **Access billentyűk** lehetőséget, és másolja az egyik kapcsolati karakterláncot. Helyezze a kapcsolati karakterláncot a szövegfájlba a megfelelő SET parancshoz. 

Most már a SET parancsokkal rendelkező fájlban vannak a környezeti változók, és tudja, hogy mik a parancssori argumentumai. Futtassuk le a mintát.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>A mintaalkalmazás futtatása és parancssori argumentumok használata

1. Nyisson meg egy parancsablakot. Válassza a Windows `command prompt` lehetőséget, és írja be a parancssorablak lekérése.

1. Másolja a vágólapra a környezeti változókat beállító parancsokat egyenként, és illessze be őket a parancssorablakba, és válassza az Enter lehetőséget. Ha végzett, írja `SET` be a parancssori ablakot a környezeti változók és azok értékeinek megtekintéséhez. Miután ezeket a parancssorablakba másolta, nem kell újra másolnia őket, hacsak nem nyit meg egy új parancssorablakot.

1. A parancssorablakban módosítsa a könyvtárakat, amíg be nem kell tenni a ./ImportExportDevicesSample fájlba (ahol az ImportExportDevicesSample.csproj fájl létezik). Ezután írja be a következőket, és adja meg a parancssori argumentumokat.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    A dotnet parancs létrehozza és futtatja az alkalmazást. Mivel az alkalmazás futtatásakor adja át a beállításokat, az alkalmazás minden egyes futtatásakor módosíthatja azok értékeit. Előfordulhat például, hogy egyszer szeretné futtatni, és új eszközöket létrehozni, majd újra futtatni, és átmásolni ezeket az eszközöket egy új hubra, és így tovább. Az összes lépést ugyanebben a futtatásban is végrehajthatja, bár azt javasoljuk, hogy ne tegyünk törlésre egyetlen eszközt sem, amíg nem biztos abban, hogy befejezte a klónozást. Íme egy példa, amely létrehoz 1000 eszközt, majd másolja őket a másik hubra.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Miután meggyőződött arról, hogy az eszközök másolása sikeresen megtörtént, a következőkkel eltávolíthatja az eszközöket a forráselosztóból:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>A mintaalkalmazás futtatása a Visual Studio használatával

1. Ha az alkalmazást a Visual Studióban szeretné futtatni, módosítsa az aktuális könyvtárat arra a mappára, amelyben az IoTHubServiceSamples.sln fájl található. Ezután futtassa ezt a parancsot a parancssorablakban a megoldás Visual Studio-ban való megnyitásához. Ezt ugyanabban a parancsablakban kell megtennie, ahol a környezeti változókat beállítja, hogy ezek a változók ismertek legyenek.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Kattintson a jobb gombbal az *ImportExportDevicesSample projektre,* és válassza **a Készlet indítási projektként parancsot.**    
    
1. Állítsa be a változók at the top of Program.cs in the ImportExportDevicesSample folder for the five options.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget. Miután befejezte a futást, megtekintheti az eredményeket.

### <a name="view-the-results"></a>Eredmények megtekintése 

Megtekintheti az eszközöket az [Azure Portalon,](https://portal.azure.com) és ellenőrizze, hogy az új helyen vannak-e.

1. Nyissa meg az új központot az [Azure Portal](https://portal.azure.com)használatával. Válassza ki a hubot, majd az **IoT-eszközök**lehetőséget. Láthatja az okat az eszközöket, amelyeket a régi hubról a klónozott hubra másolt. A klónozott hub tulajdonságait is megtekintheti. 

1. Ellenőrizze az importálási/exportálási hibákat az [Azure-tárfiókhoz](https://portal.azure.com) `devicefiles` az Azure `ImportErrors.log`Portalon, és keresse meg a tárolóban a. Ha ez a fájl üres (a mérete 0), nem voltak hibák. Ha többször próbálja importálni ugyanazt az eszközt, az másodszor utasítja el az eszközt, és hibaüzenetet ad a naplófájlhoz.

### <a name="committing-the-changes"></a>A módosítások véglegesítése 

Ezen a ponton másolta a hubot az új helyre, és áttelepítette az eszközöket az új klónba. Most módosításokat kell végrehajtania, hogy az eszközök működjenek a klónozott hubbal.

A módosítások véglegesítéséhez kövesse a végrehajtandó lépéseket: 

* Frissítse az egyes eszközöket az IoT Hub állomásnevének módosításához, hogy az IoT Hub állomásnevét az új hubra irányítsa. Ezt ugyanazzal a módszerrel kell megtennie, amelyet az eszköz első kiépítésekor használt.

* Módosítsa a régi hubra hivatkozó alkalmazásokat, hogy az új hubra mutasson.

* Miután végzett, az új hubnak működésbe kell indulnia. A régi hubnak nem kell aktív eszközökkel rendelkeznie, és leválasztott állapotban kell lennie. 

### <a name="rolling-back-the-changes"></a>A módosítások visszaállítása

Ha úgy dönt, hogy visszavonja a módosításokat, az alábbi lépéseket kell végrehajtania:

* Frissítse az egyes eszközöket az IoT Hub hostname módosításához, hogy a régi hub IoT Hub hostname-ja mutasson. Ezt ugyanazzal a módszerrel kell megtennie, amelyet az eszköz első kiépítésekor használt.

* Módosítsa az okat az alkalmazásokat, amelyek az új hubra hivatkoznak, és a régi hubra mutatnak. Ha például az Azure Analytics szolgáltatást használja, előfordulhat, hogy újra kell konfigurálnia az [Azure Stream Analytics-bemenetet.](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)

* Törölje az új elosztót. 

* Ha útválasztási erőforrásokkal rendelkezik, a régi hub konfigurációjának továbbra is a megfelelő útválasztási konfigurációra kell mutatnia, és a hub újraindítása után működnie kell ezekkel az erőforrásokkal.

### <a name="checking-the-results"></a>Az eredmények ellenőrzése 

Az eredmények ellenőrzéséhez módosítsa az IoT-megoldás, hogy pont a hub az új helyen, és futtassa azt. Más szóval hajtsa végre ugyanazokat a műveleteket az előző hubbal végrehajtott új hubbal, és győződjön meg arról, hogy azok megfelelően működnek. 

Ha már megvalósított a művelettervezést, ellenőrizze, hogy az üzenetek megfelelően vannak-e átirányítva az erőforrásokhoz.

## <a name="clean-up"></a>Tisztítás

Ne tisztítsa meg, amíg nem biztos abban, hogy az új hub működik és az eszközök megfelelően működnek. Is győződjön meg róla, hogy tesztelje az útválasztást, ha használja ezt a funkciót. Ha készen áll, az alábbi lépések végrehajtásával tisztítsa meg a régi erőforrásokat:

* Ha még nem tette meg, törölje a régi hub. Ezzel eltávolítja az összes aktív eszközt a hubról.

* Ha vannak útválasztási erőforrásai, amelyeket az új helyre helyezett át, törölheti a régi útválasztási erőforrásokat.

## <a name="next-steps"></a>További lépések

Egy IoT-központot klónozott egy új régióban, az eszközökkel együtt. Ha többet szeretne tudni atömeges műveletek ioT-központ identitásainak importálásáról és exportálásáról, további információt az [IoT Hub-eszközidentitások tömeges importálása és exportálása című témakörben](iot-hub-bulk-identity-mgmt.md)talál.

Az IoT Hubról és a hub fejlesztéséről az alábbi cikkekben talál további információt.

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)

* [IoT Hub útválasztási oktatóanyag](tutorial-routing.md)

* [IoT Hub-eszközkezelés – áttekintés](iot-hub-device-management-overview.md)

* Ha telepíteni szeretné a mintaalkalmazást, olvassa el a [.NET Core alkalmazás telepítését.](https://docs.microsoft.com/dotnet/core/deploying/index)
