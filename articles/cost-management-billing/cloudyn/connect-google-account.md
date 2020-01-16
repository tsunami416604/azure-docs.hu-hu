---
title: Google Cloud Platform-fiók összekötése az Azure-beli Cloudyn | Microsoft Docs
description: Google Cloud Platform fiók összekapcsolásával megtekintheti a Cloudyn-jelentésekben szereplő költségeket és használati adatokat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 8849811560c7e3d35a40b4725dbbb63c82745123
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994570"
---
# <a name="connect-a-google-cloud-platform-account"></a>Google Cloud Platform-fiók összekötése

Meglévő Google Cloud Platform-fiókját összekapcsolhatjuk a Cloudyn. A fiók Cloudyn való összekapcsolását követően a Cost és a használati adatok a Cloudyn-jelentésekben érhetők el. Ez a cikk segítséget nyújt a Google-fiók konfigurálásához és a Cloudyn-mel való összekapcsolásához.


## <a name="collect-project-information"></a>Projekt adatainak összegyűjtése

Először gyűjtsön információkat a projektről.

1. Jelentkezzen be a Google Cloud Platform-konzolra [https://console.cloud.google.com](https://console.cloud.google.com)címen.
2. Tekintse át a Cloudyn bevezetésre szánt projekt adatait, és jegyezze fel a **projekt nevét** és a **projekt azonosítóját**. Az információkat később is megtarthatja a későbbi lépésekhez.  
    ![a projekt nevét és a projekt AZONOSÍTÓját a Google Cloud Platform-konzolon](./media/connect-google-account/gcp-console01.png)
3. Ha a számlázás nincs engedélyezve és csatolva van a projekthez, hozzon létre egy számlázási fiókot. További információkért lásd: [új számlázási fiók létrehozása](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Storage-gyűjtő számlázási exportálásának engedélyezése

A Cloudyn egy Storage-gyűjtőből kéri le a Google számlázási adatait. A Cloudyn-regisztráció során a **gyűjtő neve** és a **jelentés előtagja** információi hasznosak maradnak a későbbi használatra.

A Google Cloud Storage használata a használati jelentések tárolására a minimális díjakat eredményezi. További információ: [felhőalapú tárolás díjszabása](https://cloud.google.com/storage/pricing).

1. Ha nem engedélyezte a számlázási exportálást egy fájlba, kövesse a [Számlázási Exportálás fájlba való engedélyezésének](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)utasításait. JSON-vagy CSV-számlázási exportálási formátumot is használhat.
2. Ellenkező esetben a Google Cloud Platform-konzolon navigáljon **a számlázás > a** **Számlázási exportálás**elemre. Jegyezze fel a számlázási **gyűjtő nevét** és a **jelentés előtagját**.  
    a számlázási Exportálás lapon megjelenő számlázási adatok ![](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform API-k engedélyezése

A használati és adategységek adatainak gyűjtéséhez a Cloudyn a következő Google Cloud Platform API-kat kell engedélyezni:

- BigQuery API
- Google Cloud SQL
- Google Cloud adattár API
- Google Cloud Storage
- Google Cloud Storage – JSON API
- Google számítási Motor API

### <a name="enable-or-verify-apis"></a>API-k engedélyezése vagy ellenőrzése

1. A Google Cloud Platform-konzolon válassza ki a Cloudyn regisztrálni kívánt projektet.
2. Navigáljon az API-k **& Services** > **Library**weblapjára.
3. A keresés funkció használatával megkeresheti az egyes korábban felsorolt API-ket.
4. Az egyes API-k esetében ellenőrizze, hogy az **API engedélyezve** van-e. Egyéb esetben kattintson az **Engedélyezés**gombra.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Google Cloud-fiók felvétele a Cloudyn-be

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a [https://azure.cloudyn.com ra](https://azure.cloudyn.com/) , és jelentkezzen be.
2. Kattintson a **Beállítások** (fogaskerék szimbólum) elemre, majd válassza a **Cloud accounts (felhőalapú fiókok**) lehetőséget.
3. A **fiókok kezelése**lapon válassza a **Google-fiókok** lapot, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **Google-fiók neve**mezőben adja meg a Számlázási fiók e-mail-címét, majd kattintson a **tovább**gombra.
5. A Google-hitelesítés párbeszédpanelen válasszon ki vagy adjon meg egy Google-fiókot, majd **engedélyezze** a cloudyn.com hozzáférését a fiókjához.
6. Adja hozzá a kérelemhez korábban feljegyzett projekthez tartozó adatokat. Ide tartozik a **projekt azonosítója**, a **projekt** neve, a **Számlázási** gyűjtő neve és a **Számlázási fájl** jelentésének előtagja, majd kattintson a **Save (Mentés**) gombra.  
    ![Google-projekt hozzáadása a Cloudyn-fiókhoz](./media/connect-google-account/add-project.png)

A Google-fiók megjelenik a fiókok listájában, és azt a **hitelesítettnek**kell lennie. Ebben az esetben a Google-projekt nevét és AZONOSÍTÓját kell megjelennie, és rendelkeznie kell egy zöld pipa jellel. A fiók állapotának **befejezettnek**kell lennie.

A Cloudyn-jelentések néhány órán belül megjelenítik a Google költségeit és használati adatait.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a Cloudyn-ről, folytassa a használati és Cloudyn kapcsolatos oktatóanyag [áttekintésével](tutorial-review-usage.md) .
