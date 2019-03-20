---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: f55bdc774437d280db51fb69f060bea7dc579a26
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203736"
---
## <a name="set-up-your-project"></a>A projekt beállítása

Biztosan ehelyett töltse le ezt a mintát Android Studio project? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip), és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a kódminta azt végrehajtása előtt.

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg az Android Studiót, és válassza ki **fájl** > **új** > **új projekt**.
2. Adjon nevet az alkalmazásnak, és válassza ki **tovább**.
3. Válassza ki **API 21 vagy újabb (Android 5.0-s)**, majd válassza ki **tovább**.
4. Hagyja **üres tevékenység** , válassza ki a **tovább**, majd válassza ki **Befejezés**.

### <a name="add-msal-to-your-project"></a>Az MSAL hozzáadása a projekthez

1. Az Android Studióban válassza a **Gradle-szkriptek** > **build.gradle (modul: alkalmazás)**.
2. A **függőségek**, illessze be a következő kódot:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>A csomaggal kapcsolatos

A fenti kód a csomag telepíti a Microsoft hitelesítési tár (MSAL), amely kezeli az összes jogkivonat műveleteket, köztük a beszerzéséhez, gyorsítótárazás, frissítése és törlése. A jogkivonatok az API-k a Microsoft identity platform által védett eléréséhez szükséges.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Az alkalmazás felhasználói felület létrehozása

1. Lépjen a **res** > **elrendezés**, majd nyissa meg **activity_main.xml**.
2. A tevékenység elrendezésének módosítása `android.support.constraint.ConstraintLayout` vagy az egyéb `LinearLayout`.
3. Adja hozzá a `android:orientation="vertical"` tulajdonságot a `LinearLayout` csomópont.
4. Illessze be az alábbi kódot a `LinearLayout` csomópontot, és cserélje le a jelenlegi tartalomról:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
