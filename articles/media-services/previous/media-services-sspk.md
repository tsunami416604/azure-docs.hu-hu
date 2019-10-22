---
title: A Microsoft® Smooth Streaming ügyféloldali Porting Kit licencelése
description: Ismerkedjen meg a Microsoft® Smooth Streaming ügyfél-Porting Kit licencelésével.
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
ms.openlocfilehash: 3e7742e7035f3c4f4827a2edae1ad5a705aa221f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693417"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>A Microsoft® Smooth Streaming ügyféloldali Porting Kit licencelése 
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming ügyféloldali Porting Kit (**SSPK** ) egy Smooth streaming ügyfél-implementáció, amely a beágyazott eszközök, a kábelek és a mobileszközök, a tartalomszolgáltatók, a kézibeszélő-gyártók, valamint a mobileszköz-szolgáltatók támogatására van optimalizálva. független szoftvergyártók (ISV-ket) és a megoldások szolgáltatói számára, hogy Smooth Streaming formátumban hozzanak létre az adaptív tartalmakat. A SSPK olyan Smooth Streaming-ügyfél eszköz-és platform-független implementációja, amelyet a licenccel bármely eszközre és platformra lehet átadni. 

Az alábbiakban látható egy magas szintű architektúra és IIS-Smooth Streaming a bevezetési készlet a Microsoft által biztosított Smooth Streaming ügyfél-implementáció, amely tartalmazza az Smooth Streaming-tartalmak lejátszásához szükséges összes alapvető logikát. Ezt a tartalmat egy adott eszközhöz vagy platformhoz tartozó partnerek a megfelelő felületek bevezetésével egy adott eszközhöz vagy platformhoz csatolják. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Leírás
A SSPK a kiváló üzleti értékkel rendelkező feltételekkel rendelkezik. A SSPK-licenc a következőket biztosítja az iparág számára:

* Smooth Streaming a Porting Kit forrásaC++ 
  * Smooth Streaming-ügyfél funkcióinak implementálása
  * formázási elemzés, heurisztikus, pufferelési logika stb.
* Player Application API-k 
  * programozási felületek a Media Player alkalmazással való interakcióhoz
* Platform absztrakciós réteg (PAL) felülete 
  * programozási felületek az operációs rendszerrel való interakcióhoz (szálak, szoftvercsatornák)
* Hardverabsztrakciós réteg (HAL) felülete 
  * programozási felületek a hardver A/V-dekóderekkel való interakcióhoz (dekódolás, renderelés)
* Digitális Rights Management (DRM) interfész 
  * a DRM kezelésére szolgáló programozási felületek a DRM absztrakciós réteg (DAL) használatával
  * A Microsoft PlayReady-portok különálló, de ezen a felületen keresztül integrálva vannak. A Microsoft PlayReady-eszközök licencelésével kapcsolatos további információkért kattintson [ide](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Megvalósítási minták 
  * minta PAL implementáció Linux rendszerhez
  * a GStreamer minta HAL-implementációja

## <a name="licensing-options"></a>Licencelési lehetőségek
A Microsoft Smooth Streaming ügyfél-előfizetési csomag elérhető két különálló licencszerződés keretében a licencek számára: az egyik a Smooth Streaming ügyfél ideiglenes termékeinek fejlesztése, valamint a Smooth Streaming ügyfél végleges termékeinek végfelhasználók számára történő terjesztése.

* A chipset-gyártók, rendszerintegrátorok vagy független szoftvergyártók (ISV-ket) esetében, akiknek a forráskód-előkészítési csomaggal kell rendelkezniük az ideiglenes termékek fejlesztéséhez, a Microsoft Smooth Streaming ügyfél-előkészítő csomag **ideiglenes licencét** kell végrehajtani.
* Azokhoz az eszközökhöz és szoftvergyártók számára, akik terjesztési jogokat igényelnek Smooth Streaming ügyfél végső termékeinek a végfelhasználók számára, a Microsoft Smooth Streaming ügyfél-telepítési csomag **végső termékének licencét** kell végrehajtani.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming ügyféloldali Porting Kit – ideiglenes termék licence
A jelen licenc keretében a Microsoft egy Smooth Streaming ügyfél-bekötési készletet és a szükséges szellemi tulajdoni jogokat biztosít Smooth Streaming-ügyfél köztes termékek fejlesztéséhez és terjesztéséhez más Smooth Streaming ügyfél-Porting Kit-eszközökre, amelyek Smooth Streaming-ügyfél végleges termékeinek terjesztése.

#### <a name="fee-structure"></a>Díj szerkezete
Az USA $50 000-es egyszeri licencelési díja hozzáférést biztosít a Smooth Streaming ügyfél-Porting Kit-hez. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming ügyféloldali Porting Kit végleges termék licence
A jelen licenc keretében a Microsoft minden szükséges szellemi tulajdonjogot biztosít ahhoz, hogy Smooth streaming ügyfél ideiglenes termékeit fogadja más Smooth streaming ügyfél-előfizetések, és a vállalat által Smooth streaming ügyfél végleges terjesztéséhez. Termékek a végfelhasználók számára.

#### <a name="fee-structure"></a>Díj szerkezete
A Smooth Streaming ügyfél végleges termékét a következők szerint kínáljuk a jogdíj-modell keretében:

* $0,10/eszköz megvalósításának elszállítása
* A jogdíj minden évben $50 000-kor van korlátozva
* Az első 10 000-es eszköz implementációja minden évben díjmentes 

## <a name="licensing-procedure-and-sspk-access"></a>Licencelési eljárás és SSPK-hozzáférés
E-mail- [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) az összes licencelési lekérdezéshez.

A [SSPK terjesztési portál](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) elérhető a regisztrált közbenső licencek számára.

Az ideiglenes és a végső SSPK-licencek technikai kérdéseket küldhetnek [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>A Microsoft Smooth Streaming ügyfél ideiglenes termék-licencszerződése

* Ügyes Business Solutions, Inc
* Speciális digitális szórásos biztonsági társítás
* AirTies Kablosuz Iletism Sanayive dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC multimédia szoftver Co., Ltd.
* Cavium, Inc.
* EchoStar-vásárlási vállalat
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Sáfrányos digitális korlátozott
* Szecsuán Changhong, Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics (Huizhou) Co., Ltd.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>A Microsoft Smooth Streaming ügyfél végleges termékre vonatkozó licencszerződései
* Speciális digitális szórásos biztonsági társítás
* AirTies Kablosuz Iletism Sanayive dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONIK SAN. VE TIC. A. Asszonyvására
* Brit Sky-műsorszolgáltatás korlátozott
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan digitális AV Technology Corp., Ltd.
* EchoStar-vásárlási vállalat
* Enseo, Inc.
* FilmFlex Movies Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier információ-pályázó S. R. L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Narancssárga SA
* Sáfrányos digitális korlátozott
* Sagemcom broadband SAS
* Shenzhen Chuangwei – RGB Elektronika Co., Ltd.
* Shenzheni együttes elektronika CO., LTD.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Szecsuán Changhong, Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* TCL tengerentúli marketing (Makaó kereskedelmi offshore) Limited
* Technicolor kézbesítési technológiák, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation/Slovakia/s.r.o.
* VEZÍR, Inc.
* Tud Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

