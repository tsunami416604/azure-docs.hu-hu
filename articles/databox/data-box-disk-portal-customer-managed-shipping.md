---
title: Microsoft Azure Data Box Disk önfelügyelt szállítás | AdatMicrosoft Docs
description: A Azure Data Box Disk eszközök önfelügyelt szállítási munkafolyamatát ismerteti
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: c4b3479e5728c32e66bc40f950bc948bf61dce42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575163"
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
* Dél-afrikai Köztársaság
* India (előzetes verzió)

## <a name="use-self-managed-shipping"></a>Önállóan felügyelt szállítás használata

Data Box Disk rendelés elhelyezésekor kiválaszthatja az önállóan felügyelt szállítási lehetőséget.

1. A Azure Data Box Diski sorrendben a **kapcsolattartási adatok**területen válassza a **+ szállítási címek hozzáadása**elemet.

   ![Képernyőkép a megrendelés varázslóról, amely a kapcsolattartási adatok lépést mutatja, a szállítási címek hozzáadása lehetőséggel.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. A szállítási típus kiválasztásakor válassza az **önfelügyelt szállítási** lehetőséget. Ez a beállítás csak akkor érhető el, ha az előfeltételek szakaszban leírtak szerint támogatott régióban van.

3. Miután megadta a szállítási címet, ellenőriznie kell, és el kell végeznie a rendelést.

   ![Képernyőkép a szállítási címek hozzáadása párbeszédpanelről, amelyen a szállítás lehetőség és a szállítási címek hozzáadása lehetőség látható.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Ha az eszköz elkészült, és e-mailben értesítést kapott, beütemezhet egy felvételt. A Azure Data Box Diski sorrendben válassza az **Áttekintés** , majd a **felvételi ütemterv**lehetőséget.

   ![Data Box-eszköz megrendelése a felvételi szolgáltatáshoz](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Kövesse az Azure-ba való **felvétel menetrendje**című témakör utasításait. Az engedélyezési kód beszerzése előtt e-mailben kell [adbops@microsoft.com](mailto:adbops@microsoft.com) megadnia az eszköz felvételét a régió adatközpontjában.

   ![Csomagfelvétel ütemezése](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Miután ütemezte az eszköz felvételét, megtekintheti az Azure-ban az  **ütemezési feladásban**megjelenő engedélyezési kódot.

   ![Képernyőkép az Azure-beli felvétel ütemezett feladatairól a beléptetési szövegmezőhöz tartozó engedélyezési kóddal.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

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

   ![Képernyőkép a szállítási címek hozzáadása párbeszédpanelről, amely a kiválasztási lehetőségekkel és a szállítási címek hozzáadása lehetőséggel van elnevezve.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Az azonosító és az engedélyezési kód ellenőrzése után a rendszer eldobta az eszközt az adatközpontban, a megrendelés állapotát meg kell **kapnia**.

    ![A fogadás befejeződött](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Az eszköz fogadása után az Adatmásolás folytatódni fog. A másolás befejezésekor a rendelés elkészült.

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: Azure Data Box Disk üzembe helyezése a Azure Portal használatával](data-box-disk-quickstart-portal.md)
