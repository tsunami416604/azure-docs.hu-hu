---
title: "Az Azure Mobile Engagement Android SDK speciális konfiguráció"
description: "Ismerteti, beleértve az Azure Mobile Engagement Android SDK-val Android Manifest speciális konfigurációs beállítás"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Az Azure Mobile Engagement Android SDK speciális konfiguráció
> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Ez az eljárás ismerteti az Azure Mobile Engagement Android-alkalmazások beállításainak konfigurálása.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Engedélykövetelmények
Néhány beállításhoz konkrét engedélyeket, amelyek az itt felsorolt hivatkozást, és beágyazott adott funkciójában szükségesek. Ezek az engedélyek hozzáadása a projekt AndroidManifest.xml azonnal előtt vagy után a `<application>` címke.

Az engedély kódot kell tűnik a következőt, ahol ki a megfelelő engedélyekkel az alábbi táblázat a.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Engedély | Használata esetén |
| --- | --- |
| INTERNET |Kötelező. Alapszintű jelentéskészítéshez |
| ACCESS_NETWORK_STATE |Kötelező. Alapszintű jelentéskészítéshez |
| RECEIVE_BOOT_COMPLETED |Kötelező. Az értesítések központ megjelennek az eszköz újraindítását követően. |
| WAKE_LOCK |Ajánlott. Lehetővé teszi, hogy Wi-Fi használata esetén, vagy ha a képernyőn a rendszer nem végez adatgyűjtést |
| MOZOG |Választható. Lehetővé teszi, hogy vibráció értesítések fogadásakor. |
| DOWNLOAD_WITHOUT_NOTIFICATION |Választható. Lehetővé teszi, hogy Android nagy vonalakban tekinti értesítés |
| WRITE_EXTERNAL_STORAGE |Választható. Lehetővé teszi, hogy Android nagy vonalakban tekinti értesítés |
| ACCESS_COARSE_LOCATION |Választható. Lehetővé teszi a valós idejű hely jelentéskészítési |
| ACCESS_FINE_LOCATION |Választható. Lehetővé teszi, hogy helyét GPS-alapú jelentés |

Kezdődően az Android M [futás közben bizonyos engedélyek felügyelt](mobile-engagement-android-location-reporting.md#android-m-permissions).

Ha már használja a ``ACCESS_FINE_LOCATION``, akkor nem kell is ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Android konfigurációs beállítások
### <a name="crash-report"></a>Összeomlási jelentést
Összeomlási jelentések letiltásához adja hozzá ezt a kódot között a `<application>` és `</application>` címkék:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Küszöbérték-kapacitásnövelés
Alapértelmezés szerint a bevonási service-jelentéseken naplók valós időben. Ha a jelentés alkalmazásnaplók változnak gyakran, érdemes meghajtóin a naplókat, és hogy jelentse őket egyszerre egy alap rendszeres időközönként a (más néven "nagy sebességű átvitel"). Ehhez adja hozzá ezt a kódot között a `<application>` és `</application>` címkék:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Kapacitásnövelés mód némileg növeli az eszközakkumulátor élettartamának, de hatással van a bevonási figyelő: minden munkamenetek és feladatok időtartama a kapacitásnövelés küszöbértéket (így munkamenetek és feladatok rövidebb, mint a kapacitásnövelés küszöbértéket nem láthatják) vannak kerekítve. A kapacitásnövelés küszöbértéknek legfeljebb 30000 (30s) kell lennie.

### <a name="session-timeout"></a>Munkamenet időkorlátja
 Egy tevékenység fejezheti billentyűkombináció lenyomásával a **Home** vagy **vissza** kulcs, vagy egy másik alkalmazásban átugorja úgy, hogy a telefon üresjárati. Alapértelmezés szerint a munkamenet véget ér 10 másodperc után utolsó tevékenysége végén. Ezzel elkerülhető a munkamenet valószínűségét minden alkalommal, amikor a felhasználó kilép, és visszaadja az alkalmazásnak gyorsan, amely képes hiba akkor fordulhat elő, amikor a felhasználó szerzi be a képen egy értesítés, stb. Érdemes lehet módosíthatják ezt a paramétert. Ehhez adja hozzá ezt a kódot között a `<application>` és `</application>` címkék:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Naplózási jelentések letiltásához
### <a name="using-a-method-call"></a>Metódushívások használatával
Ha azt szeretné, hogy leállítja a naplók küldése Engagement, hívása:

    EngagementAgent.getInstance(context).setEnabled(false);

Ez a hívás az állandó: egy megosztott beállításokat tartalmazó fájlt használja.

Ha Engagement aktív Ez a függvény hívásakor, a szolgáltatás leállítása egy percig is eltarthat. Azonban ez nem indítsa el a szolgáltatás minden legközelebb indítani az alkalmazást.

Engedélyezheti a reporting újra működik meghívásával napló `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integráció saját`PreferenceActivity`
Helyett ezt a funkciót, integrálhatja ezt a beállítást a közvetlenül a meglévő `PreferenceActivity`.

Konfigurálhatja a bevonási a beállításokat tartalmazó fájlt (a kívánt mód) a használatára a `AndroidManifest.xml` fájlt `application meta-data`:

* A `engagement:agent:settings:name` kulcs használatával a közös beállításokat fájl nevének meghatározásához.
* A `engagement:agent:settings:mode` kulcs használatával a közös beállításokat fájl módjának meghatározása. Az azonos módot, és a használja a `PreferenceActivity`. Egy számot a módot kell átadni: használatakor a konstans jelzők kombinációja a kódban, ellenőrizze a teljes értékét.

Bevonási mindig használja a `engagement:key` logikai kulcs ezzel a beállítással kezelésére szolgáló beállítások fájlon belül.

A következő példa `AndroidManifest.xml` megjeleníti az alapértelmezett értékeket:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Ezután hozzáadhatja a `CheckBoxPreference` a preferencia elrendezésben hasonló a következő:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
