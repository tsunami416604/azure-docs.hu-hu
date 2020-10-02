---
title: Azure AD Connect Cloud kiépítési attribútum-szerkesztő
description: Ez a cikk az attribútum-szerkesztő használatát ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637219"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Felhőalapú kiépítési attribútum tartozik leképezése Azure AD Connect

Azure AD Connect a Cloud kiépítés új funkciót vezetett be, amely lehetővé teszi az attribútumok egyszerű leképezését a helyszíni felhasználói/csoport objektumai és az Azure AD objektumai között.  Ez a szolgáltatás hozzá lett adva a felhő üzembe helyezési konfigurációjához.

Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is.  A szinkronizált attribútumok listáját lásd: [szinkronizált attribútumok](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Az attribútum-leképezési típusok ismertetése
Az attribútum-hozzárendelésekkel szabályozhatja, hogy az attribútumok hogyan legyenek feltöltve az Azure AD-ben.
Négy különböző leképezési típus támogatott:

- **Direct (közvetlen** ) – a TARGET attribútum a csatolt objektum attribútumának értékével van feltöltve az ad-ben.
- **Konstans** – a TARGET attribútum egy megadott karakterlánccal van feltöltve.
- **Kifejezés** – a cél attribútum a parancsfájl-szerű kifejezés eredménye alapján van feltöltve.
  További információ: [kifejezések írása attribútum-leképezésekhez](reference-expressions.md).
- **Nincs** – a cél attribútum változatlan marad. Ha azonban a cél attribútum még mindig üres, akkor a rendszer a megadott alapértelmezett értékkel tölti fel.

A négy alaptípussal együtt az egyéni attribútum-hozzárendelések az opcionális **alapértelmezett** érték-hozzárendelés fogalmát támogatják. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a TARGET attribútum értéke akkor legyen feltöltve, ha nincs érték az Azure AD-ben vagy a TARGET objektumon. A leggyakoribb beállítás az, ha üresen hagyja ezt a beállítást.

## <a name="understanding-attribute-mapping-properties"></a>Attribútumok – megfeleltetési tulajdonságok ismertetése

Az előző szakaszban már be lett vezetve az attribútum-leképezési típus tulajdonságra.
A tulajdonsággal együtt az attribútum-hozzárendelések a következő attribútumokat is támogatják:

- **Forrásoldali attribútum** – a felhasználói attribútum a forrásoldali rendszerből (példa: Active Directory).
- **Target attribútum** – a felhasználói attribútum a célszámítógépen (példa: Azure Active Directory).
- **Alapértelmezett érték, ha Null (nem kötelező)** – a célként megadott rendszernek átadandó érték, ha a forrás attribútum értéke null. Ez az érték csak a felhasználó létrehozásakor lesz kiépítve. Egy meglévő felhasználó frissítésekor az "alapértelmezett érték, ha null" nem lesz kiépítve.  
- **A leképezés alkalmazása**
  - **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is.
  - **Csak a létrehozás során** – alkalmazza ezt a leképezést csak a felhasználói létrehozási műveletekre.

> [!NOTE]
> Ez a dokumentum azt ismerteti, hogyan használható a Azure Portal az attribútumok leképezéséhez.  További információ a gráf használatáról: [átalakítások](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Attribútumok leképezésének használata
Az új funkció használatához kövesse az alábbi lépéseket.

 1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
 2.  Válassza a **Azure ad Connect**lehetőséget.
 3.  Válassza a **kiépítés kezelése**lehetőséget.

   ![Kiépítés kezelése](media/how-to-configure/manage1.png)
 
 4. A **konfiguráció**területen válassza ki a konfigurációt.
 5. Válassza **a kattintson ide a leképezések szerkesztéséhez**.  Ekkor megnyílik az attribútum-leképezési képernyő.

 ![Attribútumok hozzáadása](media/how-to-attribute-mapping/mapping6.png)
 6.  Kattintson az **attribútum hozzáadása**lehetőségre.

 ![Leképezés típusa](media/how-to-attribute-mapping/mapping1.png)
 
 7. Válassza ki a **leképezés típusát**.  Ebben a példában a kifejezést használjuk.
 8.  Adja meg a kifejezést a mezőben.  Ebben a példában a következőt használjuk: `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  Adja meg a cél attribútumot.  Ebben a példában a ExtensionAttribute15-t használjuk.
 10. Válassza ki, hogy mikor alkalmazza ezt a beállítást, majd kattintson az **alkalmaz** gombra.
   
   ![Leképezések szerkesztése](media/how-to-attribute-mapping/mapping2a.png)
 11. Az attribútum-leképezési képernyőn vissza kell látnia az új attribútum-hozzárendelést.  
 12. Kattintson a **séma mentése**gombra.

 ![Séma mentése](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>Az attribútumok leképezésének tesztelése
Az attribútumok leképezésének teszteléséhez igény [szerinti üzembe](how-to-on-demand-provision.md)helyezést is használhat.  A 

1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
2.  Válassza a **Azure ad Connect**lehetőséget.
3.  Válassza a **kiépítés kezelése**lehetőséget.
4. A **konfiguráció**területen válassza ki a konfigurációt.
5. Az **ellenőrzés** alatt kattintson a **felhasználó kiépítése** gombra. 
6. Az igény szerinti üzembe helyezési képernyőn.  Adja meg a felhasználó vagy csoport **megkülönböztető nevét** , majd kattintson a **kiépítés** gombra.  
7. Ha befejeződik, megjelenik egy sikeres képernyő és 4 zöld jelölőnégyzet, amely azt jelzi, hogy a rendszer sikeresen kiépített.  
  ![Sikeres kiépítés](media/how-to-attribute-mapping/mapping4.png)
1. A **művelet végrehajtása** alatt kattintson a **részletek megtekintése**elemre.  A jobb oldalon az új attribútum syncrhonized és az alkalmazott kifejezés látható.

  ![Művelet végrehajtása](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
- [Kifejezések írása attribútum-leképezésekhez](reference-expressions.md)
- [Szinkronizált attribútumok](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)