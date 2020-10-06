---
title: Rövid útmutató – telefonszám beszerzése az Azure kommunikációs szolgáltatásokból
description: Megtudhatja, hogyan vásárolhat kommunikációs szolgáltatások telefonszámát a Azure Portal használatával.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: e06c3720e180c1dc4fa2f227fd86d15cbbb0ff33
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756920"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Gyors útmutató: telefonszám beszerzése a Azure Portal használatával

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure Communication Services használatának első lépései a Azure Portal használatával vásárolhatják meg a telefonszámot.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktív kommunikációs szolgáltatások erőforrása.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Telefonszám beszerzése

A számok kiosztásának megkezdéséhez nyissa meg a kommunikációs szolgáltatások erőforrást a [Azure Portalon](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

### <a name="getting-new-phone-numbers"></a>Új telefonszámok beolvasása

Navigáljon a telefonszámok panelre az erőforrás menüben.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

A `Get` varázsló elindításához kattintson a gombra. A panelen `Phone numbers` megjelenő varázsló végigvezeti egy olyan kérdésen, amely segít kiválasztani a forgatókönyvhöz legjobban illő telefonszámot. 

Először ki kell választania, hogy `Country/region` Hol szeretné kiépíteni a telefonszámot. Miután kiválasztotta az országot/régiót, ki kell választania az `use case` igényeinek legmegfelelőbb lakosztállyal. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

### <a name="select-a-phone-plan"></a>Válasszon egy telefonos csomagot

A telefonos csomag kiválasztása két lépésből áll: 

1. A [szám típusának](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) kiválasztása
2. A [csomag](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services) kiválasztása

Jelenleg két típusú típust kínálunk: `Geographic` és `Toll-free` . Ha kiválasztott egy számot, a rendszer több csomagot is kínál, amelyek közül választhat.

A példánkban kiválasztottunk egy `Toll-free` számot a `Outbound calling` és a `Inbound and Outbound SMS` csomagokkal.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

Innen kattintson a `Next: Numbers` lap alján található gombra a kiépíteni kívánt telefonszám (ok) testreszabásához.

### <a name="customizing-phone-numbers"></a>Telefonszámok testreszabása

Az `Numbers` oldalon testreszabhatja a kiépíteni kívánt telefonszám (oka) t.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

> [!NOTE]
> Ez a rövid útmutató a `Toll-free` szám típusának testreszabási folyamatát mutatja be. Ha kiválasztotta a számot, a felhasználói élmény némileg eltérő lehet `Geographic` , de a végeredmény ugyanaz lesz.

Válassza ki a `Area code` listából az elérhető körzetszámot, és adja meg a kiépíteni kívánt mennyiséget, majd kattintson ide a `Search` kiválasztott követelményeknek megfelelő számok megkereséséhez. Az igényeinek megfelelő telefonszámokat a havi költséggel együtt fogjuk megjeleníteni.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

> [!NOTE]
> A rendelkezésre állás a típustól, a helytől és a kiválasztott tervtől függ.
> A szám a tranzakció lejárta előtt rövid ideig van lefoglalva. Ha a tranzakció lejár, akkor újra ki kell választania a számokat.

A vásárlás összegzésének megtekintéséhez és a megrendelés megrendeléséhez kattintson az `Next: Summary` oldal alján található gombra.

### <a name="place-order"></a>Megrendelés

Az összefoglalás lapon áttekintheti a telefonszámok kiépítéséhez szükséges számokat, szolgáltatásokat, telefonszámokat és a teljes havi költséget.

> [!NOTE]
> A feltüntetett díjak a **havi ismétlődő díjak** , amelyek a kiválasztott telefonszám bérletének költségeit fedezik. Ez a nézet nem része az utólagos elszámolású **költségeknek** , amelyek a hívások kezdeményezése vagy fogadása során merülnek fel. Az árlisták [itt érhetők el](../../concepts/pricing.md). Ezek a költségek a meghívott szám típusától és célhelytől függenek. Például, ha egy Seattle-beli régióból érkező hívást egy New York-beli regionális számra hív át, és az azonos számú, Egyesült királyságbeli mobil telefonszámra irányuló hívás eltérő lehet.

Végül kattintson a `Place order` lap alján a megerősítéshez.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Telefonszámok megkeresése a Azure Portal

Navigáljon az Azure kommunikációs erőforrásához a [Azure Portalon](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

A telefonszámok kezeléséhez válassza a menüben a telefonszámok panelt.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

> [!NOTE]
> Eltarthat néhány percig, amíg a kiépített számok megjelennek ezen az oldalon.

### <a name="customizing-phone-number-plans"></a>Telefonszám-csomagok testreszabása
A `Numbers` lapon kiválaszthatja a telefonszámot, ha arra a számra kattint, amelynek testre szeretné szabni a csomagot.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

Válassza ki a lehetőségeket az elérhető hívási és SMS-funkciók listájából, majd kattintson a `Confirm` kijelölés alkalmazásához.

## <a name="troubleshooting"></a>Hibaelhárítás

Gyakori kérdések és problémák:

- Jelenleg csak az USA-beli telefonszámok megvásárlása támogatott. Ez annak az előfizetésnek a számlázási címe alapján történik, amelyhez az erőforrás társítva van. Jelenleg nem lehet áthelyezni egy erőforrást egy másik előfizetésbe.

- Ha telefonszámot szabadít fel, a telefonszámot a rendszer nem szabadítja fel, illetve nem tudja újra megvásárolni a számlázási időszak végéig.

- A kommunikációs szolgáltatások erőforrásának törlésekor a rendszer automatikusan felszabadítja az adott erőforráshoz tartozó telefonszámokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta a következőket:

> [!div class="checklist"]
> * Telefonszám vásárlása
> * Telefon számának kezelése
> * Telefonszám felszabadítása

> [!div class="nextstepaction"]
> [SMS küldése](../telephony-sms/send.md) 
>  [Ismerkedés a hívással](../voice-video-calling/getting-started-with-calling.md)
