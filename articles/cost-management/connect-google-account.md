---
title: A Google Cloud Platform fiók csatlakozhat az Azure-ban a Cloudyn |} A Microsoft Docs
description: Költség- és használati adatok megtekintéséhez a Cloudyn jelentésekben a Google Cloud Platform-fiók csatlakoztatásához.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 09035146fe3211f9fc46d3ad51326a6e76921b7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977298"
---
# <a name="connect-a-google-cloud-platform-account"></a>A Google Cloud Platform-fiók csatlakoztatása

A Cloudyn csatlakozhat a Google Cloud Platform-fiókját. Miután csatlakozott a fiók a Cloudyn, Cloudyn jelentésekben költség- és használati adatok érhető el. Ez a cikk segít beállítása és csatlakozása a Google-fiókját a Cloudynre.

## <a name="collect-project-information"></a>Projekt adatainak gyűjtése

Első lépésként a projekttel kapcsolatos információk összegyűjtéséhez.

1. Jelentkezzen be a Google Cloud Platform kezelőpultját [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Tekintse át a Cloudyn, és jegyezze fel a bevezetni kívánt projekt adatokat a **projektnév** és a **Projektazonosító**. Biztosíthatja az adatok későbbi lépéseire praktikus.  
    ![Google Cloud Platform-konzol](./media/connect-google-account/gcp-console01.png)
3. Ha a számlázás nincs engedélyezve, és a projekthez kapcsolódó, számlázási fiók létrehozása. További információkért lásd: [hozzon létre egy új számlázási fiók](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Tárolási gyűjtőbe számlázási Exportálás engedélyezése

A Cloudyn lekérdezi a Google-számlázási adatok tárolási kérelemegységeket. Tartsa a **gyűjtőbe neve** és **jelentés előtag** információ hasznos későbbi használatra a Cloudyn-regisztráció során.

Google Cloud Storage segítségével tárolja a használati jelentések tekintetében a minimális díjak. További információkért lásd: [Cloud Storage díjszabása](https://cloud.google.com/storage/pricing).

1. Ha nem engedélyezte a számlázási exportálása fájlba, kövesse az utasításokat, [egy számlázási Exportálás engedélyezése](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Használhatja a JSON és a fürt megosztott kötetei szolgáltatás számlázási exportálási formátumát.
2. Ellenkező esetben a Google Cloud Platform-konzolon lépjen **számlázási** > **számlázási exportálási**. Vegye figyelembe a számlázási **gyűjtőbe neve** és **jelentés előtag**.  
    ![A számlázás exportálása](./media/connect-google-account/billing-export.png)

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
2. Navigáljon a **API-k és szolgáltatások** > **könyvtár**.
3. Keresés segítségével megkeresheti a korábban felsorolt minden egyes API-t.
4. Minden API-hoz, ellenőrizze, hogy **API engedélyezve** jelenik meg. Ellenkező esetben kattintson a **engedélyezése**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adjon hozzá egy Google Cloud Cloudyn

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy navigáljon arra [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) , és jelentkezzen be.
2. Kattintson a **beállítások** (fogaskerék szimbólum), majd **Felhőfiókok**.
3. A **fiókkezelés**, jelölje be a **Google-fiókok** fülre, majd **új hozzáadása +**.
4. A **Google-fiók neve**, adja meg a számlázási fiók e-mail címét, majd kattintson a **tovább**.
5. A Google-hitelesítési párbeszédpanelen válassza ki, vagy adjon meg egy Google-fiókot, majd **engedélyezése** cloudyn.com hozzáférést a fiókjához.
6. Adja meg a kérelem projekt információkat, hogy Ön volt-e korábbi feljegyzett. Tartalmazzák **Projektazonosító**, **projekt** neve, **számlázási** gyűjtőbe nevét, és **számlázási fájl** előtag jelentést, majd kattintson a  **Mentés**.  
    ![Google-projekt hozzáadása](./media/connect-google-account/add-project.png)

A Google-fiók megjelenik a fiókok listáját, és üzenetnek kell megjelennie **hitelesített**. Azt a Google-projekt neve és azonosítója kell jelennek meg, és rendelkezik egy zöld pipa szimbólumot. Fiók állapota a következő: **befejezve**.

Néhány órán belül a Cloudyn-jelentések megjelenítése a Google költség- és használati adatok.

## <a name="next-steps"></a>További lépések

- A Cloudyn kapcsolatos további információkért folytassa az [tekintse át a használat és költségek](./tutorial-review-usage.md) Cloudyn oktatóanyagot.
