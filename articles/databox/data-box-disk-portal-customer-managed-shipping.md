---
title: Microsoft Azure Data Box Disk önfelügyelt szállítás | AdatMicrosoft Docs
description: A Azure Data Box Disk eszközök önfelügyelt szállítási munkafolyamatát ismerteti
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 70c37b60d3b01c939b9b06d6b053b526e036904a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266508"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>A Azure Portal Azure Data Box Disk önfelügyelt szállítás használata

Ez a cikk a Azure Data Box Disk megrendeléséhez, felvételéhez és eldobásához szükséges önálló felügyelt szállítási feladatokat ismerteti. A Data Box Disk a Azure Portal használatával kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Az önfelügyelt szállítás a [Azure Data Box Disk megrendelése](data-box-disk-deploy-ordered.md)esetén választható. Az önállóan felügyelt szállítás csak a következő régiókban érhető el:

* Egyesült Államok – Államigazgatás
* Nyugat-Európa
* Japán
* Szingapúr
* Dél-Korea

## <a name="use-self-managed-shipping"></a>Önállóan felügyelt szállítás használata

Data Box Disk rendelés elhelyezésekor kiválaszthatja az önállóan felügyelt szállítási lehetőséget.

1. A Azure Data Box Diski sorrendben a **kapcsolattartási adatok**területen válassza a **+ szállítási címek hozzáadása**elemet.

   ![Saját üzemeltetésű szállítás](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. A szállítási típus kiválasztásakor válassza az **önfelügyelt szállítási** lehetőséget. Ez a beállítás csak akkor érhető el, ha az előfeltételek szakaszban leírtak szerint támogatott régióban van.

3. Miután megadta a szállítási címet, ellenőriznie kell, és el kell végeznie a rendelést.

   ![Saját üzemeltetésű szállítás](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Ha az eszköz elkészült, és e-mailben értesítést kapott, beütemezhet egy felvételt. A Azure Data Box Diski sorrendben válassza az **Áttekintés** , majd a **felvételi ütemterv**lehetőséget.

   ![Data Box-eszköz megrendelése a felvételi szolgáltatáshoz](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Kövesse az Azure-ba való **felvétel menetrendje**című témakör utasításait. Az engedélyezési kód beszerzése előtt e-mailben kell [adbops@microsoft.com](mailto:adbops@microsoft.com) megadnia az eszköz felvételét a régió adatközpontjában.

   ![Csomagfelvétel ütemezése](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Miután ütemezte az eszköz felvételét, megtekintheti az Azure-ban az **ütemezési feladásban**megjelenő engedélyezési kódot.

   ![Az engedélyezési kód megtekintése](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Jegyezze fel ezt az **engedélyezési kódot**.

   A biztonsági követelményeknek megfelelően az ütemezési felveszés időpontjában meg kell adni annak a személynek a nevét, aki a beléptetésre fog érkezni.

   Azt is meg kell adnia, hogy ki fog szolgálni az adatközpontba. Önnek vagy a kapcsolattartási pontnak a kormány által jóváhagyott, az adatközpontban érvényesített fénykép-AZONOSÍTÓval kell rendelkeznie.

   Emellett az eszköz beszedését végző személynek is rendelkeznie kell az **engedélyezési kóddal**. Az engedélyezési kód egyedi egy felvételhez vagy egy leálláshoz, és az adatközpontban érvényesítve van.

7. A megrendelés automatikusan a **kiválasztott** állapotba kerül, amint az eszközt az adatközpontból felvette.

   ![Szállítás alatt](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Az eszköz beléptetése után az adatait átmásolhatja a hely Data Box Disk (ok) ra. Az Adatmásolás befejezése után előkészítheti a Data Box Disk szállítását.

   Az adatok másolásának befejezése után kapcsolatba kell lépnie a műveletekkel, hogy ütemezzen egy találkozót a legördülő listából. Meg kell osztania az adatközpontba érkező személy adatait, hogy eldobják a lemezeket. Az adatközpontnak emellett ellenőriznie kell az engedélyezési kódot a leálláskor. Az eldobáshoz tartozó engedélyezési kód Azure Portal az **ütemezett legördülő listából**elérhető lesz.

   > [!NOTE]
   > Ne ossza meg az engedélyezési kódot e-mailben. Ezt csak az adatközpontban lehet ellenőrizni a legördülő listából.

9. Ha olyan időpontot kapott, amely a legördülő listára való, most már **készen áll az Azure-adatközpont állapotának fogadására** a Azure Portal.

   ![Az engedélyezési kód megtekintése](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Az azonosító és az engedélyezési kód ellenőrzése után a rendszer eldobta az eszközt az adatközpontban, a megrendelés állapotát meg kell **kapnia**.

    ![A fogadás befejeződött](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Az eszköz fogadása után az Adatmásolás folytatódni fog. A másolás befejezésekor a rendelés elkészült.

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: Azure Data Box Disk üzembe helyezése a Azure Portal használatával](data-box-disk-quickstart-portal.md)
