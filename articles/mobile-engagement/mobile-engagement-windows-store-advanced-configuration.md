---
title: "Speciális Windows univerzális alkalmazások Engagement SDK konfigurációja"
description: "Speciális konfigurációs beállítások, az Azure Mobile Engagement az univerzális Windows-alkalmazások"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Speciális Windows univerzális alkalmazások Engagement SDK konfigurációja
> [!div class="op_single_selector"]
> * [Univerzális Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

Ez az eljárás ismerteti az Azure Mobile Engagement Android-alkalmazások beállításainak konfigurálása.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Speciális konfiguráció
### <a name="disable-automatic-crash-reporting"></a>Automatikus összeomlási jelentések letiltása
A jelentéskészítési funkció bekapcsolása automatikus összeomlási letilthatja. Majd amikor nem kezelt kivétel történik, a bevonási nincs semmi hatása.

> [!WARNING]
> Ha letiltja ezt a szolgáltatást, akkor az alkalmazás egy nem kezelt összeomlás esetén Engagement nem küldi el a összeomlási **és** nem zárja be a munkamenet és feladatok.
> 
> 

Automatikus összeomlási jelentések letiltásához alakítható attól függően, hogy Ön deklarált azt módja:

#### <a name="from-engagementconfigurationxml-file"></a>A `EngagementConfiguration.xml` fájl
Jelentés összeomlási beállítása `false` közötti `<reportCrash>` és `</reportCrash>` címkék.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A `EngagementConfiguration` futási időben objektum
A jelentés összeomlási értéke HAMIS, a EngagementConfiguration objektum használatával.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Tiltsa le a valós idejű reporting
Alapértelmezés szerint a bevonási service-jelentéseken naplók valós időben. Ha az alkalmazás naplók gyakran, érdemes meghajtóin a naplók és, hogy azok egyszerre rendszeres időközönként alapon. Ennek elnevezése "kapacitásnövelés mód".

Ehhez a metódus meghívására:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Az argumentum értéke a **ezredmásodperc**. Amikor a valós idejű naplózási újraaktiválni kívánt, metódushívás a bármely paraméter nélkül, vagy a 0 értéket.

Kapacitásnövelés mód némileg növeli az eszközakkumulátor élettartamának, de hatással van a bevonási figyelő: minden munkamenetek és feladatok időtartama a kapacitásnövelés küszöbértéket (így munkamenetek és feladatok rövidebb, mint a kapacitásnövelés küszöbértéket nem láthatják) vannak kerekítve. A 30000 (30s) mint kapacitásnövelés küszöbérték használatát javasoljuk. Mentett naplókat 300 elemet korlátozódnak. Ha a küldő túl hosszú, néhány naplók elveszhet.

> [!WARNING]
> Nem lehet konfigurálni a kapacitásnövelés küszöbértéket, egy kisebb, mint egy második. Ha így tesz, az SDK-t jeleníti meg a hiba: a nyomkövetés, és automatikusan visszaállítja az alapértelmezett érték nulla másodperc. Ezzel elindítja az SDK jelentheti a naplók valós időben.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
