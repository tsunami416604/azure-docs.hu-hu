---
title: A Google Cloud Platform fiók csatlakozhat az Azure-ban a Cloudyn |} A Microsoft Docs
description: Költség- és használati adatok megtekintéséhez a Cloudyn jelentésekben a Google Cloud Platform-fiók csatlakoztatásához.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230128"
---
# <a name="connect-a-google-cloud-platform-account"></a>A Google Cloud Platform-fiók csatlakoztatása

A Cloudyn csatlakozhat a Google Cloud Platform-fiókját. Miután csatlakozott a fiók a Cloudyn, Cloudyn jelentésekben költség- és használati adatok érhető el. Ez a cikk segít beállítása és csatlakozása a Google-fiókját a Cloudynre.


## <a name="collect-project-information"></a>Projekt adatainak gyűjtése

Első lépésként a projekttel kapcsolatos információk összegyűjtéséhez.

1. Jelentkezzen be a Google Cloud Platform-konzolra [https://console.cloud.google.com](https://console.cloud.google.com)címen.
2. Tekintse át a Cloudyn bevezetésre szánt projekt adatait, és jegyezze fel a **projekt nevét** és a **projekt azonosítóját**. Biztosíthatja az adatok későbbi lépéseire praktikus.  
    ![a projekt nevét és a projekt AZONOSÍTÓját a Google Cloud Platform-konzolon](./media/connect-google-account/gcp-console01.png)
3. Ha a számlázás nincs engedélyezve, és a projekthez kapcsolódó, számlázási fiók létrehozása. További információkért lásd: [új számlázási fiók létrehozása](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Tárolási gyűjtőbe számlázási Exportálás engedélyezése

A Cloudyn lekérdezi a Google-számlázási adatok tárolási kérelemegységeket. A Cloudyn-regisztráció során a **gyűjtő neve** és a **jelentés előtagja** információi hasznosak maradnak a későbbi használatra.

Google Cloud Storage segítségével tárolja a használati jelentések tekintetében a minimális díjak. További információ: [felhőalapú tárolás díjszabása](https://cloud.google.com/storage/pricing).

1. Ha nem engedélyezte a számlázási exportálást egy fájlba, kövesse a [Számlázási Exportálás fájlba való engedélyezésének](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)utasításait. Használhatja a JSON és a fürt megosztott kötetei szolgáltatás számlázási exportálási formátumát.
2. Ellenkező esetben a Google Cloud Platform-konzolon navigáljon **a számlázás > a** **Számlázási exportálás**elemre. Jegyezze fel a számlázási **gyűjtő nevét** és a **jelentés előtagját**.  
    a számlázási Exportálás lapon megjelenő számlázási adatok ![](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>A Google Cloud Platform API-k engedélyezése

Szoftverleltárazási és használati adatokat gyűjthet, a Cloudyn a következő Google Cloud Platform API-k engedélyezve van szüksége:

- BigQuery API
- Google Cloud SQL
- A Google Cloud Datastore API
- Google Cloud Storage
- A Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Engedélyezi, vagy ellenőrizze az API-k

1. A Google Cloud Platform-konzolon válassza ki a projektet, a Cloudyn regisztrálni kívánt.
2. Navigáljon az API-k **& Services** > **Library**weblapjára.
3. Keresés segítségével megkeresheti a korábban felsorolt minden egyes API-t.
4. Az egyes API-k esetében ellenőrizze, hogy az **API engedélyezve** van-e. Egyéb esetben kattintson az **Engedélyezés**gombra.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adjon hozzá egy Google Cloud Cloudyn

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a [https://azure.cloudyn.comra](https://azure.cloudyn.com/) , és jelentkezzen be.
2. Kattintson a **Beállítások** (fogaskerék szimbólum) elemre, majd válassza a **Cloud accounts (felhőalapú fiókok**) lehetőséget.
3. A **fiókok kezelése**lapon válassza a **Google-fiókok** lapot, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **Google-fiók neve**mezőben adja meg a Számlázási fiók e-mail-címét, majd kattintson a **tovább**gombra.
5. A Google-hitelesítés párbeszédpanelen válasszon ki vagy adjon meg egy Google-fiókot, majd **engedélyezze** a cloudyn.com hozzáférését a fiókjához.
6. Adja meg a kérelem projekt információkat, hogy Ön volt-e korábbi feljegyzett. Ide tartozik a **projekt azonosítója**, a **projekt** neve, a **Számlázási** gyűjtő neve és a **Számlázási fájl** jelentésének előtagja, majd kattintson a **Save (Mentés**) gombra.  
    ![Google-projekt hozzáadása a Cloudyn-fiókhoz](./media/connect-google-account/add-project.png)

A Google-fiók megjelenik a fiókok listájában, és azt a **hitelesítettnek**kell lennie. Azt a Google-projekt neve és azonosítója kell jelennek meg, és rendelkezik egy zöld pipa szimbólumot. A fiók állapotának **befejezettnek**kell lennie.

Néhány órán belül a Cloudyn-jelentések megjelenítése a Google költség- és használati adatok.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a Cloudyn-ről, folytassa a használati és Cloudyn kapcsolatos oktatóanyag [áttekintésével](./tutorial-review-usage.md) .
