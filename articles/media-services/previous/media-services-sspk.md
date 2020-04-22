---
title: A&reg; Microsoft Smooth Streaming ügyfélportolási készlet ének lebonyolítása
description: További információ a Microsoft&reg; Smooth Streaming ügyfélportingkészlet licenceléséről.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: bd77c53a195a9549f6aaad9ee2928206f5324b0c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686897"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>A&reg; Microsoft Smooth Streaming ügyfélportolási készlet ének lebonyolítása 
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming Client Porting Kit **(röviden SSPK)** egy sima streamelésű ügyfélimplementáció, amely a beágyazott eszközgyártók, a kábel- és mobilszolgáltatók, a tartalomszolgáltatók, a készülékgyártók, a független szoftvergyártók (ISV-k) és a megoldásszolgáltatók számára optimalizált zökkenőmentes streamelési formátumban történő folyamatos streamelési termékek és szolgáltatások létrehozásához van optimalizálva. Az SSPK a Smooth Streaming ügyfél eszköz- és platformfüggetlen implementációja, amelyet az engedélyes bármely eszközre és platformra továbbíthat. 

Az alábbiakban egy magas szintű architektúra és az IIS Smooth Streaming Porting Kit doboz a Microsoft által biztosított Smooth Streaming ügyfél implementáció, amely tartalmazza a Smooth Streaming tartalom lejátszásának alapvető logikáját. Ezt a tartalmat a partnerek egy adott eszközre vagy platformra portolták a megfelelő felületek megvalósításával. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Leírás
Az SSPK licence kiváló üzleti értéket kínál. Az SSPK-licenc a következőket biztosítja az iparág számára:

* Sima streamelési portolási készlet forrása C++ nyelven 
  * zökkenőmentes streamelési ügyfél funkciót valósít meg
  * hozzáadja a formátumelemzés, a heurisztika, a pufferelési logika stb.
* Player alkalmazás API-k 
  * programozási felületek a médialejátszó alkalmazással való interakcióhoz
* Platform absztrakciós réteg (PAL) felület 
  * programozási felületek az operációs rendszerrel való interakcióhoz (szálak, aljzatok)
* Hardverabsztrakciós réteg (HAL) felülete 
  * programozási felületek a hardveres A/V dekóderrel való interakcióhoz (dekódolás, renderelés)
* Digitális jogkezelési (DRM) interfész 
  * programozási felületek a DRM drm-nek a DRM absztrakciós rétegen (DAL) keresztül történő kezeléséhez
  * A Microsoft PlayReady Porting Kit külön-külön szállít, de ezen a felületen keresztül integrálható. A Microsoft PlayReady eszköz licencelésről további részletekért kattintson [ide](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Megvalósítási minták 
  * minta PAL megvalósítás a Linux
  * minta HAL megvalósítása a GStreamer számára

## <a name="licensing-options"></a>Licencelési beállítások
A Microsoft Smooth Streaming ügyfélporting készlet két különböző licencszerződés alapján érhető el az engedélyesek számára: az egyik a Smooth Streaming ügyfél ideiglenes termékeinek fejlesztésére, a másik pedig a Smooth Streaming client final products végfelhasználók számára történő terjesztésére.

* A köztes termékek kifejlesztéséhez forráskód-portolási készletet igénylő lapkakészlet-gyártók, rendszerintegrátorok vagy független szoftverszállítók (ISV-k) esetében végre kell hajtani a Microsoft Smooth Streaming ügyfélportingkészlet **ideiglenes terméklicencét.**
* Azon eszközgyártók és független szoftvergyártók számára, akiknek terjesztési jogokra van szükségük a Smooth Streaming client final products végfelhasználói számára, végre kell hajtani a Microsoft Smooth Streaming client porting kit **final product licencet.**

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming ügyfél portolási készlet ideiglenes terméklicenc
E licenc alapján a Microsoft egy smooth streaming ügyfélporting készletet és a smooth streaming ügyfél ideiglenes termékek fejlesztéséhez és terjesztéséhez szükséges szellemi tulajdonjogokat kínál a Smooth Streaming Client Interim Products fejlesztéséhez és terjesztéséhez a Smooth Streaming Client Porting Kit eszköz licencbevevői számára, amelyek smooth streaming ügyfél végső termékeket terjesztenek.

#### <a name="fee-structure"></a>Díjstruktúra
Az Egyesült Államok $50,000 egyszeri licencdíj hozzáférést biztosít a Smooth Streaming client porting kit.A US $50,000 egyszeri licenc fee provides access to the Smooth Streaming Client Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming ügyfél portolási készlet – végső termék licenc
A jelen licenc értelmében a Microsoft minden szükséges szellemi tulajdonjogot biztosít a Smooth Streaming Ügyfél ideiglenes termékeinek fogadására más Smooth Streaming ügyfélporting készlet licencbe vevőktől, valamint a vállalat márkájú Smooth Streaming Client Final Products terjesztésére a végfelhasználók számára.

#### <a name="fee-structure"></a>Díjstruktúra
A Smooth Streaming Client Final Product egy jogdíjmodell alatt érhető el, a következők szerint:

* $0.10 eszközimplementációnként leszállított
* A jogdíj felső határa 50.000 $ évente
* Nincs jogdíj az első 10 000 eszközimplementációhoz évente 

## <a name="licensing-procedure-and-sspk-access"></a>Engedélyezési eljárás és SSPK-hozzáférés
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) az összes licencelési lekérdezéshez.

Az SSPK terjesztési portál elérhető a regisztrált ideiglenes licenccel rendelkezők számára.

Az ideiglenes és a végleges SSPK [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)engedélyesek technikai kérdéseket nyújthatnak be a rendszernek.

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming ügyfél ideiglenes termékszerződés-licencbevevői

* Adroit Üzleti Megoldások, Inc
* Fejlett digitális műsorszórás SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Kft.
* Alticast Vállalat
* Amazon Digital Services, Inc.
* Arion Technológia, Inc.
* AVC Multimédia Szoftver Co., Ltd.
* Cavium, Inc.
* EchoStar Beszerzési Vállalat
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Zrt.
* Liberty Global Szolgáltatások BV
* A MediaTek Zrt.
* MStar Co, Kft.
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Sáfrány Digital Limited
* Szecsuán Changhong Electric Co., Ltd.
* SoftAtHome között
* Sony Vállalat
* Tatung Technology Inc.
* Top Victory Investments, Kft.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Vállalat

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming ügyfél végleges termékszerződés licencbe vevői
* Fejlett digitális műsorszórás SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Kft.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technológiai Vállalat
* Arion Technológia, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Kft.
* CastPal Technology Inc., Shenzhen
* Compal Elektronika, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Beszerzési Vállalat
* Enseo, Inc.
* FilmFlex Filmek Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovációk Limited
* Haier Információ kérelmező S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Kft.
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Vállalat
* Nintendo Co., Ltd.
* Narancssárga SA
* Sáfrány Digital Limited
* Sagemcom szélessávú SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd.
* Shenzhen Skyworth Digital Technology Co., Ltd.
* Szecsuán Changhong Electric Co., Ltd.
* Skardin Ipari Vállalat.
* Sky Deutschland Fernsehen GmbH &.
* SmarDTV S.A.
* SoftAtHome között
* Sony Vállalat
* Technicolor szállítási technológiák, SAS
* Tongfang Globális Kft.
* Top Victory Investments, Kft.
* Toshiba Lifestyle Termékek & Services Corporation
* Universal Media Corporation /Szlovákia/ s.r.o.
* VIZIO, Inc.
* Wistron Vállalat
* ZTE Vállalat

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

