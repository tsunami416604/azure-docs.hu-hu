---
title: Azure-igazolás beállítása Azure Portal
description: Igazolási szolgáltató beállítása és konfigurálása Azure Portal használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 52c0dd6825c8576d97e10d3f0568ca5fb81e789e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210817"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Gyors útmutató: Azure-igazolás beállítása Azure Portal

Az alábbi lépések végrehajtásával felügyelheti az igazolási szolgáltatót Azure Portal használatával.

## <a name="attestation-provider"></a>Igazolási szolgáltató

### <a name="create-an-attestation-provider"></a>Tanúsító szolgáltató létrehozása

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>A szolgáltató beállítása aláíratlan házirendekkel

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza az **erőforrás létrehozása** lehetőséget.
2.  A keresőmezőbe írja be az **igazolás** kifejezést.
3.  Az eredmények listából válassza a **Microsoft Azure igazolás** elemet.
4.  Az Microsoft Azure igazolás lapon válassza a **Létrehozás** lehetőséget.
5.  Az igazoló szolgáltató létrehozása lapon adja meg a következő bemeneti adatokat:
    
    **Előfizetés**: válasszon egy előfizetést
    
    **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét
    
    **Név**: egyedi név megadása kötelező

    **Hely**: válasszon egy helyet 
    
    **Házirend-aláíró tanúsítvány fájlja**: ne töltse fel a házirend-aláíró tanúsítványok fájlját a szolgáltató aláíratlan házirendekkel való konfigurálásához 
6.  A szükséges bemenetek megadása után kattintson a **felülvizsgálat + létrehozás** gombra.
7.  Javítsa ki az érvényesítési problémákat, és kattintson a **Létrehozás** gombra.

#### <a name="to-configure-the-provider-with-signed-policies"></a>A szolgáltató konfigurálása aláírt házirendekkel

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza az **erőforrás létrehozása** lehetőséget.
2.  A keresőmezőbe írja be az **igazolás** kifejezést.
3.  Az eredmények listából válassza a **Microsoft Azure igazolás** elemet.
4.  Az Microsoft Azure igazolás lapon válassza a **Létrehozás** lehetőséget.
5.  Az igazoló szolgáltató létrehozása lapon adja meg a következő információkat:
    
    a. **Előfizetés**: válasszon egy előfizetést
    
    b. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét
    
    c. **Név**: egyedi név megadása kötelező

    d. **Hely**: válasszon egy helyet 
    
    e. **Házirend-aláíró tanúsítvány fájlja**: az igazolási szolgáltató házirend-aláírási tanúsítványokkal való konfigurálásához, a tanúsítványok fájljának feltöltéséhez. Tekintse meg [a példákat](/azure/attestation/policy-signer-examples) 
6.  A szükséges bemenetek megadása után kattintson a **felülvizsgálat + létrehozás** gombra.
7.  Javítsa ki az érvényesítési problémákat, és kattintson a **Létrehozás** gombra.

### <a name="view-attestation-provider"></a>Tanúsító szolgáltató megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét, és jelölje ki

### <a name="delete-attestation-provider"></a>Tanúsító szolgáltató törlése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Jelölje be a jelölőnégyzetet, majd kattintson a **Törlés** gombra.
4.  Írja be az Igen értéket, és kattintson a **Törlés** [vagy] gombra.
1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  A felső menüben kattintson a **Törlés** elemre, majd kattintson az **Igen** gombra.


## <a name="attestation-policy-signers"></a>Igazolási házirend aláírói

### <a name="view-policy-signer-certificates"></a>Házirend-aláíró tanúsítványok megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  Kattintson a házirend-aláíró **tanúsítványok letöltése** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatók esetében)
6.  A letöltött szövegfájl minden tanúsítványt JWS formátumban fog tartalmazni.
a.  Ellenőrizze a letöltött tanúsítványok darabszámát és tanúsítványait.

### <a name="add-policy-signer-certificate"></a>Házirend-aláíró tanúsítvány hozzáadása

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Hozzáadás** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatóknál)
6.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és kattintson a **Hozzáadás** gombra. Tekintse meg [a példákat](/azure/attestation/policy-signer-examples)

### <a name="delete-policy-signer-certificate"></a>Házirend-aláíró tanúsítvány törlése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Törlés** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatók esetében)
6.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és kattintson a **Törlés** gombra. Tekintse meg [a példákat](/azure/attestation/policy-signer-examples) 

## <a name="attestation-policy"></a>Igazolási szabályzat

### <a name="view-attestation-policy"></a>Igazolási szabályzat megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  Válassza ki az előnyben részesített **igazolási típust** , és tekintse meg az **aktuális szabályzatot**

### <a name="configure-attestation-policy"></a>Igazolási házirend konfigurálása

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Ha a tanúsító szolgáltató házirend-aláírási követelmény nélkül jön létre

##### <a name="upload-policy-in-jwt-format"></a>Szabályzat feltöltése JWT formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha a tanúsító szolgáltató házirend-aláírási követelmény nélkül jön létre, a felhasználó feltöltheti a szabályzatot **JWT** vagy **szöveges** formátumban
7.  **Házirend formátumának** kiválasztása **JWT**
8.  Az **aláíratlan/aláírt JWT** formátumban töltse fel a házirend tartalmát tartalmazó fájlt, majd kattintson a **Save (Mentés**) gombra. Tekintse meg [a példákat](/azure/attestation/policy-examples)
    
    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.

7.  A beállított szabályzat megtekintéséhez kattintson a felső menüben a **frissítés** gombra.

##### <a name="upload-policy-in-text-format"></a>Szabályzat feltöltése szöveges formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha a tanúsító szolgáltató házirend-aláírási követelmény nélkül jön létre, a felhasználó feltöltheti a szabályzatot **JWT** vagy **szöveges** formátumban
7.  **Házirend formátumának** kiválasztása **szövegként**
8.  **Szöveges** formátumban töltse fel a fájl tartalmát, vagy írja be a házirend tartalmát a szövegmezőbe, majd kattintson a **Mentés** gombra. Tekintse meg [a példákat](/azure/attestation/policy-examples)

    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.

8.  Kattintson a **frissítés** gombra a beállított szabályzat megtekintéséhez.

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Ha a tanúsító szolgáltató házirend-aláírási követelménysel jön létre

##### <a name="upload-policy-in-jwt-format"></a>Szabályzat feltöltése JWT formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha az igazolási szolgáltató házirend-aláírási követelménysel jön létre, a felhasználó csak **aláírt JWT formátumban** tölthet fel szabályzatot
7.  A feltöltési házirend fájljának **aláírása JWT** , majd kattintson a **Save (Mentés**) gombra. Tekintse meg [a példákat](/azure/attestation/policy-examples)

    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.
    
8.  Kattintson a **frissítés** gombra a beállított szabályzat megtekintéséhez.

 










