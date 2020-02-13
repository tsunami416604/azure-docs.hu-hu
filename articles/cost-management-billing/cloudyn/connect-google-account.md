---
title: Google Cloud Platform-fiók csatlakoztatása a Cloudynhez az Azure-ban | Microsoft Docs
description: Csatlakoztasson egy Google Cloud Platform-fiókot a költségek és a használati adatok Cloudyn-jelentésekben való megtekintéséhez.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: 09989337b2e78277c6182630ce0f1cdf57a0e0c1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76770189"
---
# <a name="connect-a-google-cloud-platform-account"></a>Google Cloud Platform-fiók csatlakoztatása

Csatlakoztathatja meglévő Google Cloud Platform-fiókját a Cloudynhez. Miután csatlakoztatta a fiókot a Cloudynhez, a költség- és a használati adatok elérhetők lesznek a Cloudyn-jelentésekben. Ez a cikk segítséget nyújt a Google-fiók konfigurálásához és a Cloudynhez való csatlakoztatásához.


## <a name="collect-project-information"></a>Projektadatok gyűjtése

Első lépésként gyűjtsön adatokat a projektről.

1. Jelentkezzen be a Google Cloud Platform konzoljára a [https://console.cloud.google.com](https://console.cloud.google.com) webhelyen.
2. Ellenőrizze annak a projektnek az adatait, amelyet a Cloudynben szeretne regisztrálni, és jegyezze fel a **Project name** (Projekt neve) és a **Project ID** (Projektazonosító) értékét. Ezekre az adatokra szükség lesz a későbbi lépésekben.  
    ![A Project name és a Project ID a Google Cloud Platform konzolján](./media/connect-google-account/gcp-console01.png)
3. Ha a számlázás nincs engedélyezve és a projekthez társítva, hozzon létre egy számlázási fiókot. További információ: [Új számlázási fiók létrehozása](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Tároló számlázása exportálásának engedélyezése

A Cloudyn egy tárolóból kéri le a Google számlázási adatait. Jegyezze fel a **Bucket name** (Gyűjtő neve) és a **Report prefix** (Jelentés előtagja) értékeit, mert a Cloudyn regisztrációja során szükség lesz rájuk.

A Google Cloud Storage a használati jelentések tárolására való használata minimális költséggel jár. További információ: [A felhőalapú tárolás díjszabása](https://cloud.google.com/storage/pricing).

1. Ha nem engedélyezte a számlázás fájlba történő exportálását, kövesse a következőben szereplő utasításokat: [Számlázás fájlba történő exportálásának engedélyezése](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). A számlázás exportálásához JSON és CSV formátumot is használhat.
2. Egyéb esetben lépjen a Google Cloud Platform konzoljának **Billing** > **Billing export** (Számlázás > Számlázás exportálása) területére. Jegyezze fel a számlázás **Bucket name** (Gyűjtő neve) és **Report prefix** (Jelentés előtagja) értékét.  
    ![A számlázás exportálási adatai a Billing export (Számlázás exportálása) lapon](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform API-k engedélyezése

A használat és az adategységek adatainak gyűjtéséhez a Cloudyn a következő Google Cloud Platform API-k engedélyezését igényli:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>API-k engedélyezése vagy ellenőrzése

1. A Google Cloud Platform konzolján válassza ki azt a projektet, amelyet regisztrálni szeretne a Cloudynben.
2. Lépjen az **APIs & Services** > **Library** (API-k és szolgáltatások > Kódtár) területre.
3. Keresse meg a korábban felsorolt API-kat.
4. Az egyes API-k esetében ellenőrizze, hogy az **API enabled** (API engedélyezve) érték jelenik-e meg. Ellenkező esetben kattintson az **ENABLE** (Engedélyezés) elemre.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Google Cloud-fiók hozzáadása a Cloudynhez

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a [https://azure.cloudyn.com](https://azure.cloudyn.com/) webhelyre, és jelentkezzen be.
2. Kattintson a **Settings** (Beállítások) elemre (fogaskerék szimbólum), majd válassza a **Cloud Accounts** (Cloud-fiókok) lehetőséget.
3. Az **Accounts Management** (Fiókkezelés) területen válassza a **Google Accounts** (Google-fiókok) lapot, majd kattintson az **Add new +** (Új hozzáadása +) elemre.
4. A **Google Account Name** (Google-fiók neve) mezőbe írja be a számlázási fiók e-mail-címét, majd kattintson a **Next** (Következő) gombra.
5. A Google authentication (Hitelesítés Google-fiókkal) párbeszédpanelen válassza ki vagy adja meg a Google-fiókot, majd kattintson az **ALLOW** (Engedélyezés) gombra, hogy a cloudyn.com hozzáférhessen a fiókhoz.
6. Adja meg a kért projektadatokat, amelyeket korábban feljegyzett. Ezek közé tartozik a **Project ID** (Projektazonosító), a **Project** name (Projekt neve), a **billing** bucket name (számlázási gyűjtő neve) és a **billing file** Report prefix (számlázási fájl jelentési előtagja). Kattintson a **Mentés** gombra.  
    ![Google-projekt hozzáadása Cloudyn-fiókhoz](./media/connect-google-account/add-project.png)

A Google-fiók megjelenik a fiókok listájában, ahol **Authenticated** (Hitelesített) állapotúnak kell lennie. Alatta a Google-projekt nevének és azonosítójának kell megjelennie, mellettük egy zöld pipával. A fiók állapota **Completed** (Befejezve) kell, hogy legyen.

A Cloudyn-jelentésekben néhány órán belül megtekinthetők a Google költségei és használati adatai.

## <a name="next-steps"></a>További lépések

- Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn [A használat és a költségek áttekintése](tutorial-review-usage.md) című oktatóanyagára.
