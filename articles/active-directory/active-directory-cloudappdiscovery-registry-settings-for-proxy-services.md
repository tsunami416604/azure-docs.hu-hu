---
title: "Cloud App Discovery beállításjegyzék-beállítások proxyszolgáltatást |} Microsoft Docs"
description: "Ez a témakör célja biztosítja, hogy a lépéseket kell elvégeznie a szükséges port beállítása a Cloud App Discovery-ügynököt futtató számítógépeken."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b8584809b76d6be12a6f489f0bb819081d1803d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Cloud App Discovery beállításjegyzék-beállítások Proxy szolgáltatások
Ez a témakör célja, hogyan hajtsa végre a szükséges port beállítása a Cloud App Discovery-ügynököt futtató számítógépeken. Alapértelmezés szerint a Cloud App Discovery-ügynök csak a portok 80-as vagy 443-as használatára van konfigurálva. Ha azt tervezi, a Cloud App Discovery telepítése által használt egyéni portot (80-as, sem 443-as) proxykiszolgálóval környezetben, akkor kell konfigurálni az ügynököket, a port használatára. A konfiguráció egy beállításkulcs megadásával alapul.

> [!TIP] 
> Tekintse meg az Azure Active Directory (Azure AD), amely tovább növeli a most ügynök nélküli Cloud App Discovery fejlesztések [integráció a Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Módosítsa a portot használják a Cloud App Discovery-ügynököt futtató számítógépen

1. A beállításjegyzék-szerkesztő elindításához.
  ![Futtatás](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Keresse meg, vagy hozza létre a következő beállításkulcsot: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. Hozzon létre egy új **karakterláncsoros** nevű értéket **portok**. 
  ![Új](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Lehetőségre a **Karakterláncsor szerkesztése** párbeszédpanel, kattintson duplán a **portok** érték.
5. Az a **érték**, adja meg a következő értékeket, és adja hozzá a szervezet által használt összes egyéni portokat: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Karakterláncsor szerkesztése](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Kattintson a **OK** bezárásához a **Karakterláncsor szerkesztése** párbeszédpanel.

## <a name="next-steps"></a>Következő lépések

* [Hogyan lehet használt, jóvá nem hagyott felhőalkalmazások felderítése a szervezeten belül](active-directory-cloudappdiscovery-whatis.md) 

