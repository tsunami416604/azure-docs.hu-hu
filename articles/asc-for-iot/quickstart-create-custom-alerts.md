---
title: Egyéni riasztások létrehozása az ASC az IoT-előzetes verzió |} A Microsoft Docs
description: Hozzon létre, és rendelje hozzá egyéni riasztások az ASC az IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 591000f251d384b961569f9d7ca09ae93edea617
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541642"
---
# <a name="quickstart-create-custom-alerts"></a>Gyors útmutató: Egyéni riasztások létrehozása

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egyéni biztonsági csoportok és a riasztások révén kihasználhatja a teljes körű biztonsági információkat és a kategorikus eszköz Tudásbázis annak biztosítására, a nagyobb biztonság az IoT-megoldás között. 

## <a name="why-use-custom-alerts"></a>Egyéni riasztások miért érdemes használni? 

Akkor tudja, hogy a legjobb IoT-eszközeit.

Azok a vásárlóknak, akik reklámkattintásokat várt eszköz viselkedésük IOT ASC lehetővé teszi, hogy az eszköz viselkedésének szabályzatba ismertetése fordítása, és bármilyen eltérés riasztás várt, normál működés.

## <a name="security-groups"></a>Biztonsági csoportok

Biztonsági csoportok lehetővé teszik az eszközök logikai csoportok meghatározása és a egy központosított módon kezelheti a biztonsági állapotuk.

Ezek a csoportok adott hardverekhez, az eszközök üzembe helyezett egy bizonyos helyét, vagy bármely más saját igényeinek megfelelő csoport az eszközök hozhat létre.

Biztonsági modul ikereszköz címke nevű tulajdonság által meghatározott biztonsági csoportok **SecurityGroup**. Ez az eszköz biztonsági csoport tulajdonság értékének módosítása  

Alapértelmezés szerint minden egyes IoT-megoldás az IoT hub rendelkezik nevű biztonsági csoportot **alapértelmezett**.

Biztonsági csoportok segítségével az eszközök csoport logikai kategóriákba. Miután létrehozta a csoportokat, hozzárendelheti azokat a választott, a leghatékonyabb teljes körű megoldás az egyéni riasztások. 

## <a name="customize-an-alert"></a>Riasztás testreszabása

1. Nyissa meg az IoT hubnak. 
2. Válassza ki **biztonsági**, majd **egyéni riasztások**. 
3. Válassza ki a biztonsági csoportokat a testreszabása a alkalmazni kíván. 
4. Kattintson a **egy egyéni riasztás hozzáadása**
5. Adja meg a riasztás nevét. (vegye figyelembe, hogy a riasztás neve létrehozása után nem módosítható). 
6. A legördülő listából válassza ki egy egyéni riasztás viselkedése. 
7. A szükséges tulajdonságait szerkesztheti, és kattintson a **OK**.
8. Győződjön meg arról, hogy kattintson **mentése**. Nem mentette az új riasztás, a riasztás a következő alkalommal, zárja be az IoT Hub törlődnek.

 
## <a name="alerts-available-for-customization"></a>Elérhető a testreszabáshoz riasztások

Az alábbi táblázat a testreszabáshoz elérhető riasztások összegzését tartalmazza.

| Severity | Name (Név)                                                                                                    | Adatforrás | Leírás                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Alacsony      | Egyéni riasztási - felhőszolgáltatás eszköz üzenetek az AMQP protokollt nem szerepel a megengedett tartományon          | IoT Hub     | A felhő eszköz üzeneteit (AMQP protokoll) egy olyan időkeretet mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási – elutasított felhőszolgáltatás eszköz üzenetek az AMQP protokollt nem szerepel a megengedett tartományon | IoT Hub     | A felhőbe való eszközüzenetek (AMQP protokoll) egy olyan időkeretet az eszköz által elutasított mennyisége nem szerepel a megengedett tartományon beállított |
| Alacsony      | Egyéni riasztási - eszközt a felhőbe irányuló üzenetek AMQP protokoll száma nem szerepel a megengedett tartományon          | IoT Hub     | A felhőbe irányuló üzenetek (AMQP protokoll) egy olyan időkeretet az eszköz mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási - száma közvetlen metódus meghívása nem szerepel a megengedett tartományon                              | IoT Hub     | Közvetlen metódus mennyisége elindítja az ablak nem szerepel a megengedett tartományon beállított idő                                                     |
| Alacsony      | Egyéni riasztási - fájlfeltöltések száma nem szerepel a megengedett tartományon                                       | IoT Hub     | Fájlok feltöltése egy időtartományban mennyisége nem szerepel a megengedett tartományon beállított                                                              |
| Alacsony      | Egyéni riasztás – a HTTP protokoll eszközüzenetek felhő száma nem szerepel a megengedett tartományon          | IoT Hub     | A felhő eszköz üzeneteit (HTTP-protokoll) egy olyan időkeretet mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási – elutasított felhőszolgáltatás eszköz üzeneteit a HTTP protokollt nem szerepel a megengedett tartományon | IoT Hub     | A felhőbe való eszközüzenetek (HTTP-protokoll) egy olyan időkeretet az eszköz által elutasított mennyisége nem szerepel a megengedett tartományon beállított |
| Alacsony      | Egyéni riasztási – HTTP-protokoll az üzenetek felhőből eszközre száma nem szerepel a megengedett tartományon          | IoT Hub     | A felhőbe irányuló üzenetek (HTTP-protokoll) egy olyan időkeretet az eszköz mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási - felhőszolgáltatás az MQTT protokoll eszköz üzenetek nem szerepel a megengedett tartományon          | IoT Hub     | A felhő eszköz üzeneteit (MQTT protokoll) egy olyan időkeretet mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási – elutasított felhőszolgáltatás eszköz üzenetek az MQTT protokoll nem szerepel a megengedett tartományon | IoT Hub     | A felhőbe való eszközüzenetek (MQTT protokoll) egy olyan időkeretet az eszköz által elutasított mennyisége nem szerepel a megengedett tartományon beállított |
| Alacsony      | Egyéni riasztási - MQTT protokoll az üzenetek felhőből eszközre száma nem szerepel a megengedett tartományon          | IoT Hub     | A felhőbe irányuló üzenetek (MQTT protokoll) egy olyan időkeretet az eszköz mennyisége nem szerepel a megengedett tartományon beállított                                  |
| Alacsony      | Egyéni riasztási - parancs várólista pon száma nem szerepel a megengedett tartományon                               | IoT Hub     | A parancs várólista mennyisége kiürítése ablak nem szerepel a megengedett tartományon beállított idő                                                      |
| Alacsony      | Egyéni riasztási - ikereszköz-frissítések száma nem szerepel a megengedett tartományon                                       | IoT Hub     | Egy olyan időkeretet az ikereszköz-frissítések mennyisége nem szerepel a megengedett tartományon beállított                                                              |
| Alacsony      | Egyéni riasztási – nem engedélyezett műveletek számát nem szerepel a megengedett tartományon                            | IoT Hub     | Egy olyan időkeretet jogosulatlan műveletek mennyisége nem szerepel a megengedett tartományon beállított                                                   |
| Alacsony      | Egyéni riasztási – aktív kapcsolatok nem a megengedett tartományon                                        | Ügynök       | Egy olyan időkeretet aktív kapcsolat mennyisége nem szerepel a megengedett tartományon beállított                                                        |
| Alacsony      | Egyéni riasztási - IP-címet, amely nem engedélyezett kimenő kapcsolat létrejött                              | Ügynök       | Egy kimenő kapcsolatot, amely nem engedélyezett egy IP-cím létrehozása                                                                                  |
| Alacsony      | Egyéni riasztás – helyi sikertelen bejelentkezések száma nem szerepel a megengedett tartományon                                | Ügynök       | Egy olyan időkeretet sikertelen helyi bejelentkezési mennyisége nem szerepel a megengedett tartományon beállított                                                       |
| Alacsony      | Egyéni riasztási – nem engedélyezett felhasználó bejelentkezési                                                      | Ügynök       | Az eszköz bejelentkezett egy helyi felhasználót, hogy nem engedélyezett                                                                                        |
| Alacsony      | Egyéni riasztási – nem engedélyezett egy folyamat végrehajtása                                               | Ügynök       | Egy folyamat, amely nem engedélyezett végre lett hajtva az eszközön |          |

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben talál a biztonsági ügynök telepítése...

> [!div class="nextstepaction"]
> [A biztonsági ügynök telepítése](select-deploy-agent.md)
