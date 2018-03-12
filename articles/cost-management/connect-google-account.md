---
title: "A Google Cloud Platform fiók csatlakozni Azure költség Management |} Microsoft Docs"
description: "Csatlakoztassa a Google Cloud Platform fiók költség megtekintéséhez, és használati adatok költség felügyeleti repots."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 8f8c157be0a369817099afa211015ba7587017e3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Csatlakozás a Google Cloud Platform fiók

Kapcsolódás a meglévő Google Cloud Platform fiókot Azure költség Management. Költség felügyeleti fiókja csatlakozik, a költség jelentések költség- és használati adatok érhető el. Ez a cikk segít beállítása és csatlakozása a Google-fiók költség-kezeléssel.

## <a name="collect-project-information"></a>Adatok gyűjtése

Indítsa el a projekt információt gyűjt.

1. Jelentkezzen be a Google Cloud Platform konzolhoz [https://console.cloud.google.com](https://console.cloud.google.com).
2. Tekintse át a projekt költség felügyeleti és vegye figyelembe a bevezetni kívánt adatokat a **projektnevet** és a **Projektazonosítónak**. Biztosíthatja az adatok későbbi lépéseire lesz szüksége.  
    ![Google Cloud Platform konzol](./media/connect-google-account/gcp-console01.png)
3. Ha nincs engedélyezve a számlázási van, és a projekthez kapcsolódó, hozzon létre egy számlázási fiókot. További információkért lásd: [hozzon létre egy új számlázási fiókot](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Tárolási gyűjtő számlázási Exportálás engedélyezése

Költség felügyeleti lekéri a Google, számlázási adatok egy tárolási gyűjtőjét. Tartsa a **gyűjtő neve** és **jelentés előtag** információ későbbi felhasználás céljából hasznos költség felügyeleti regisztrálás során.

Google Cloud Storage tárolja a használati jelentések használata azt eredményezi, a minimális díjak azok háromszorosa. További információkért lásd: [Cloud Storage szolgáltatás díjszabása](https://cloud.google.com/storage/pricing).

1. Ha nem engedélyezte a számlázási exportálása fájlba, kövesse az utasításokat, [egy számlázási Exportálás engedélyezése](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Használhatja a JSON-ban vagy a fürt megosztott kötetei szolgáltatás számlázási exportálási formátumát.
2. Ellenkező esetben a Google Cloud Platform konzolon navigáljon **számlázási** > **számlázási exportálás**. Vegye figyelembe a számlázási **gyűjtő neve** és **jelentés előtag**.  
    ![Számlázási exportálása](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform API-k engedélyezése

Használati és eszköz céghez, a költség felügyeleti követelményeket a következő Google Cloud Platform API-k engedélyezve:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google számítási motor alkalmazásprogramozási felülete

### <a name="enable-or-verify-apis"></a>Engedélyezze, vagy ellenőrizze az API-k

1. A Google Cloud Platform konzolban válassza a projekt költség felügyeleti regisztrálni kívánt.
2. Navigáljon a **API-k & szolgáltatások** > **könyvtár**.
3. Kereséssel minden korábban listázott API kereséséhez.
4. Minden API-hoz, ellenőrizze, hogy **API engedélyezve** jelenik meg. Ellenkező esetben kattintson a **engedélyezése**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Google Cloud-fiók hozzáadása költség-felügyelet

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra [https://azure.cloudyn.com](https://azure.cloudyn.com/) , és jelentkezzen be.
2. Kattintson a **beállítások** (fogaskerék ikonjára symbol), és válassza **felhő fiókok**.
3. A **fiókok kezelése**, jelölje be a **Google-fiókot** fülre, majd **új hozzáadása +**.
4. A **Google-fiók neve**, adja meg a számlázási fiók e-mail címét, majd kattintson az **következő**.
5. A Google hitelesítési párbeszédpanelen válassza ki, vagy írjon be egy Google-fiókba, majd **engedélyezése** cloudyn.com hozzáfér a fiókjához.
6. Adja meg a kérelem projekt információkat kellett előző nincs jelezve. Tartalmaznak **Projektazonosítónak**, **projekt** nevét, **számlázási** gyűjtő nevét, és **számlázási fájl** előtag jelentést, majd kattintson az  **Mentés**.  
    ![Google-projekt hozzáadása](./media/connect-google-account/add-project.png)

Google-fiókja fiókok listája jelenik meg, és üzenetnek kell megjelennie **hitelesített**. Azt a Google projekt nevét és Azonosítóját kell jelenik meg, és rendelkezik egy zöld pipa szimbólumot. Fiók állapota üzenetnek kell megjelennie **befejezve**.

Néhány órán belül költség jelentések megjelenítése Google költség- és használati adatokat.

## <a name="next-steps"></a>További lépések

- További információ az Azure költség Management, továbbra is a [tekintse át a használati és költségek](./tutorial-review-usage.md) oktatóanyag költség-kezelésre.
