---
title: Architektúra IoT Központi Digitális Elosztó Központ | Microsoft dokumentumok
description: A Digital Distribution Center alkalmazássablon architektúrája az IoT Central számára
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 359aaf7bbde5501716f3d8c4229b2b90918fd5de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020981"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Az IoT Central digitális elosztóközpont alkalmazássablonjának architektúrája



A partnerek és az ügyfelek az alábbi útmutatásokkal & használhatják a **digitális elosztóközpont-megoldások** at.

> [!div class="mx-imgBorder"]
> ![digitális elosztóközpont](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Telemetriai adatokat küldő IoT-érzékelők készlete egy átjáróeszközre
2. Telemetriai adatokat és összesített elemzéseket küldő átjáróeszközök az IoT Centralnak
3. Az adatok a kívánt Azure-szolgáltatáshoz irányítva lesznek a manipuláció hoz
4. Az Azure-szolgáltatások, például az ASA vagy az Azure Functions az adatfolyamok újraformázására és a kívánt tárfiókokra való elküldésre használhatók 
5. A feldolgozott adatokat a közel valós idejű műveletek vagy a hűtőtárolás érdekében a rendszer gyorstárolással tárolja, és további, a gép- vagy kötegelemzésen alapuló betekintési fejlesztéseket biztosít. 
6. A Logic Apps különböző üzleti munkafolyamatok működtetésére használható végfelhasználói üzleti alkalmazásokban

## <a name="details"></a>Részletek
A következő szakasz a koncepcionális architektúra egyes részeinek körvonalait

## <a name="video-cameras"></a>Videokamerák 
A digitálisan összekapcsolt vállalati szintű ökoszisztémában a videokamerák az elsődleges érzékelők. A gépi tanulás és a mesterséges intelligencia fejlődése, amely lehetővé teszi a videó strukturált adatokká alakítását és feldolgozását a felhőbe való küldés előtt. Ip-kamerákkal képeket készíthetünk, tömöríthetjük őket a kamerán, majd elküldhetjük a tömörített adatokat a videoelemzési folyamat szélvonalán keresztül, vagy GigE látáskamerákkal készíthetünk képeket az érzékelőn, majd elküldhetjük ezeket a képeket közvetlenül az Azure IoT Edge-nek. , amely a videóelemzési folyamat feldolgozása előtt tömörít. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT peremhálózati átjáró
A "kamerák-as-érzékelők" és a peremhálózati számítási feladatok helyileg kezeli az Azure IoT Edge és a kamera stream által feldolgozott elemzési folyamat. Az Azure IoT Edge videoelemzési feldolgozási folyamata számos előnnyel jár, beleértve a csökkentett válaszidőt, az alacsony sávszélességű felhasználást, ami alacsony késleltetést eredményez a gyors adatfeldolgozáshoz. A rendszer csak a legalapvetőbb metaadatokat, elemzéseket vagy műveleteket küldi el a felhőbe további műveletek vagy vizsgálat céljából. 

## <a name="device-management-with-iot-central"></a>Eszközkezelés az IoT Central segítségével 
Az Azure IoT Central egy megoldásfejlesztési platform, amely leegyszerűsíti az IoT-eszközt & Az Azure IoT Edge átjárókapcsolat, -konfiguráció és -kezelés. A platform jelentősen csökkenti az IoT-eszközök kezelésének, műveleteinek és a kapcsolódó fejlesztéseknek a terheit és költségeit. Az ügyfelek & partnerek teljes körű vállalati megoldásokat hozhatnak létre a digitális visszacsatolási hurok eléréséhez az elosztóközpontokban.

## <a name="business-insights-and-actions-using-data-egress"></a>Üzleti elemzések és műveletek adatforgalom használatával 
Az IoT Central platform gazdag bővíthetőségi lehetőségeket biztosít a folyamatos adatexportálás (CDE) és api-k révén. Üzleti elemzések, amelyek telemetriai adatfeldolgozáson vagy nyers telemetriai adatokon alapulnak, általában egy előnyben részesített üzletági alkalmazásba kerülnek. Webhook, Service Bus, event hub vagy blob storage segítségével érhető el a machine learning-modellek létrehozásához, betanításához és üzembe helyezéséhez, valamint az elemzések további gazdagításához.

## <a name="next-steps"></a>További lépések
* További információ a [digitális elosztóközpont-sablon telepítéséről](./tutorial-iot-central-digital-distribution-center-pnp.md)
* További információ az [IoT Central kiskereskedelmi sablonjairól](./overview-iot-central-retail-pnp.md)
* Az IoT Centralról további információ az [IoT Central áttekintése című témakörben található.](../core/overview-iot-central.md)
