---
title: Az OPC Vault tanúsítványszolgáltatás kezelése - Azure | Microsoft dokumentumok
description: Az OPC-tároló legfelső szintű hitelesítésszolgáltatói tanúsítványainak és felhasználói engedélyeinek kezelése.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203653"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Az OPC-tároló tanúsítványszolgáltatásának kezelése

Ez a cikk ismerteti az AZURE-beli OPC Vault tanúsítványkezelési szolgáltatás felügyeleti feladatait. Információkat tartalmaz a kiállító hitelesítésszolgáltatói tanúsítványok megújításáról, a visszavont tanúsítványok listájának (CRL) megújításáról, valamint a felhasználói hozzáférés megadásáról és visszavonásáról.

## <a name="create-or-renew-the-root-ca-certificate"></a>A legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása vagy megújítása

Az OPC-tároló telepítése után létre kell hoznia a legfelső szintű hitelesítésszolgáltatói tanúsítványt. Érvényes kiállítóhitelesítési tanúsítvány nélkül nem lehet aláírni vagy kikiadni az alkalmazástanúsítványokat. A tanúsítványok ésszerű és biztonságos élettartammal való kezeléséhez [a tanúsítványok című dokumentumban](howto-opc-vault-secure-ca.md#certificates) tájékodhat. A kiállító hitelesítésszolgáltatói tanúsítvány megújítása fél élettartama után. Megújításkor vegye figyelembe azt is, hogy az újonnan aláírt alkalmazástanúsítvány konfigurált élettartama nem haladhatja meg a kiállító hitelesítésszolgáltatói tanúsítvány élettartamát.
> [!IMPORTANT]
> A kiállító hitelesítésszolgáltatói tanúsítvány létrehozásához vagy megújításához rendszergazdai szerepkör szükséges.

1. Nyissa meg a `https://myResourceGroup-app.azurewebsites.net`tanúsítványszolgáltatást a alkalmazásban, és jelentkezzen be.
2. Nyissa meg a **Tanúsítványcsoportok ( Tanúsítványcsoportok**) ( Tanúsítványcsoportok ) .
3. Egy alapértelmezett tanúsítványcsoport szerepel a listában. Válassza a **Szerkesztés** elemet.
4. A **Tanúsítványcsoport részleteinek szerkesztése**területen módosíthatja a hitelesítésszolgáltató és az alkalmazástanúsítványok tulajdonosnevét és élettartamát. A tulajdonost és az élettartamot csak egyszer szabad beállítani az első hitelesítésszolgáltatói tanúsítvány kiadása előtt. A műveletek során az élettartam-módosítások a kiállított tanúsítványok és visszavont tanúsítványok listáják inkonzisztens élettartamát eredményezhetik.
5. Adjon meg érvényes tárgyat `CN=My CA Root, O=MyCompany, OU=MyDepartment`(például ) .<br>
   > [!IMPORTANT]
   > Ha módosítja a tulajdonost, meg kell újítania a kiállítói tanúsítványt, különben a szolgáltatás nem tudja aláírni az alkalmazástanúsítványokat. A konfiguráció tárgyát a rendszer összeveti az aktív kiállítói tanúsítvány tulajdonosával. Ha a tulajdonos nem egyezik, a tanúsítvány aláírása elutasításra kerül.
6. Kattintson a **Mentés** gombra.
7. Ha ezen a ponton "tiltott" hibaüzenetet észlel, a felhasználói hitelesítő adatok nem rendelkeznek rendszergazdai engedéllyel az új főtanúsítvány módosítására vagy létrehozására. Alapértelmezés szerint a szolgáltatást üzembe helyező felhasználó rendszergazdai és aláírási szerepkörrel rendelkezik a szolgáltatással. Más felhasználók hozzá kell adni a jóváhagyó, író i vagy rendszergazdai szerepkörök, adott esetben az Azure Active Directory (Azure AD) alkalmazás regisztráció.
8. Válassza a **Részletek lehetőséget.** Ennek meg kell jelennie a frissített információk között.
9. Válassza **a Hitelesítésszolgáltatói tanúsítvány megújítása** lehetőséget az első kiállító hitelesítésszolgáltatói tanúsítvány kiállításához vagy a Kiállító tanúsítvány megújításához. Ezután kattintson az **OK** gombra.
10. Néhány másodperc múlva megjelenik **a tanúsítvány részletei**. Ha le szeretné tölteni a legújabb hitelesítésszolgáltatói tanúsítványt és visszavont tanúsítványok at az OPC UA-alkalmazásokban való terjesztéshez, válassza **a Kiállító** vagy **a Crl lehetőséget.**

Most az OPC UA tanúsítványkezelő szolgáltatás készen áll az OPC UA-alkalmazások tanúsítványainak kiállítására.

## <a name="renew-the-crl"></a>A visszavont tanúsítványok listájának megújítása

A kl megújítása egy frissítés, amelyet rendszeres időközönként el kell osztani az alkalmazások között. Az OPC UA-eszközök, amelyek támogatják a CRL Distribution Point X509 bővítményt, közvetlenül frissíthetik a visszavont tanúsítványok listájának frissítését a mikroszolgáltatási végpontról. Más OPC UA-eszközök manuális frissítéseket igényelhetnek, vagy frissíthetők a GDS-kiszolgáló leküldéses bővítményei (*) használatával a megbízhatósági listák tanúsítványokkal és visszavont tanúsítványok listáival való frissítéséhez.

A következő munkafolyamatban a törölt állapotokban lévő összes tanúsítványkérelmet visszavonják a visszavont tanúsítványok listájában, amelyek megfelelnek annak a kiállító hitelesítésszolgáltatói tanúsítványnak, amelyhez kiállították őket. A visszavont tanúsítványok listájának verziószáma 1-gyel növekszik. <br>
> [!NOTE]
> Minden kiadott visszavont tanúsítványok listájá a kiállító hitelesítésszolgáltatói tanúsítvány lejártáig érvényesek. Ennek az az oka, hogy az OPC UA specifikációja nem igényel kötelező, determinisztikus terjesztési modellt a visszavont tanúsítványok listájához.

> [!IMPORTANT]
> A kiállító icr-jének megújításához rendszergazdai szerepkör szükséges.

1. Nyissa meg a `https://myResourceGroup.azurewebsites.net`tanúsítványszolgáltatást a alkalmazásban, és jelentkezzen be.
2. Nyissa meg a **Tanúsítványcsoportok** lapot.
3. Válassza a **Részletek lehetőséget.** Ennek meg kell jelenlennie az aktuális tanúsítvány- és visszavont tanúsítványok listájának információiközött.
4. Válassza **a Visszavont tanúsítványok listájának frissítése (CRL)** lehetőséget az OPC Vault tárolójában lévő összes aktív kiállítói tanúsítvány frissített visszavonási listájának kiadásához.
5. Néhány másodperc múlva megjelenik **a tanúsítvány részletei**. Ha le szeretné tölteni a legújabb hitelesítésszolgáltatói tanúsítványt és visszavont tanúsítványok at az OPC UA-alkalmazásokban való terjesztéshez, válassza **a Kiállító** vagy **a Crl lehetőséget.**

## <a name="manage-user-roles"></a>Felhasználó szerepkörök kezelése

Az Azure AD Enterprise Alkalmazásban kezelheti az OPC Vault mikroszolgáltatás felhasználói szerepköreit. A szerepkör-definíciók részletes leírását a Szerepkörök című [témakörben tetszési témakörben tetszési témakörben lehet.](howto-opc-vault-secure-ca.md#roles)

Alapértelmezés szerint a bérlő hitelesített felhasználója readerként jelentkezhet be a szolgáltatásba. Magasabb kiemelt jogosultságú szerepkörök manuális kezelést igényel nek az Azure Portalon, vagy a PowerShell használatával.

### <a name="add-user"></a>Felhasználó hozzáadása

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az **Azure Active Directory** > **Enterprise alkalmazásokat.**
3. Válassza ki az OPC Vault mikroszolgáltatás (alapértelmezés szerint a) regisztrációját. `resourceGroupName-service`
4. Nyissa meg a **Felhasználók és csoportok lehetőséget.**
5. Válassza **a Felhasználó hozzáadása**lehetőséget.
6. Jelölje ki vagy hívja meg a felhasználót egy adott szerepkörhöz való hozzárendelésre.
7. Válassza ki a felhasználók szerepkörét.
8. Válassza a **Hozzárendelés** elemet.
9. A rendszergazdai vagy jóváhagyói szerepkörben lévő felhasználók számára továbbra is adja hozzá az Azure Key Vault hozzáférési szabályzatait.

### <a name="remove-user"></a>Felhasználó eltávolítása

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az **Azure Active Directory** > **Enterprise alkalmazásokat.**
3. Válassza ki az OPC Vault mikroszolgáltatás (alapértelmezés szerint a) regisztrációját. `resourceGroupName-service`
4. Nyissa meg a **Felhasználók és csoportok lehetőséget.**
5. Jelöljön ki egy eltávolítandó szerepkörrel rendelkező felhasználót, majd válassza **az Eltávolítás gombot.**
6. A rendszergazdai vagy jóváhagyói szerepkörben eltávolított felhasználók esetében távolítsa el őket az Azure Key Vault-szabályzatokból is.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Felhasználói hozzáférési szabályzat hozzáadása az Azure Key Vaulthoz

A jóváhagyók és a rendszergazdák számára további hozzáférési házirendek szükségesek.

Alapértelmezés szerint a szolgáltatás identitása csak korlátozott engedélyekkel rendelkezik a Key Vault eléréséhez, hogy megakadályozza a magasabb szintű műveleteket vagy módosításokat a felhasználó megszemélyesítése nélkül. Az alapvető szolgáltatás engedélyek beget és lista, mind a titkos kulcsok és tanúsítványok. A titkos kulcsok esetében csak egy kivétel van: a szolgáltatás törölheti a titkos kulcsot a titkos tárolóból, miután a felhasználó elfogadta. Minden más művelethez felhasználói megszemélyesített engedélyek szükségesek.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Jóváhagyó szerepkör esetén a következő engedélyeket kell hozzáadni a Key Vaulthoz

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az `resourceGroupName`OPC-tárolót, amelyet az üzembe helyezés során használ.
3. Nyissa meg a `resourceGroupName-xxxxx`Key Vault .
4. Nyissa meg a **Hozzáférési házirendeket**.
5. Válassza **az Új hozzáadása**lehetőséget.
6. Hagyja ki a sablont. Nincs olyan sablon, amely megfelelne a követelményeknek.
7. Válassza **az Egyszerű kijelölés lehetőséget,** és jelölje ki a hozzáadni kívánt felhasználót, vagy hívjon meg egy új felhasználót a bérlőhöz.
8. Válassza ki a következő **kulcsengedélyeket:** **Get**, **List**és **Sign**.
9. Válassza ki a következő **titkos engedélyeket:** **Get**, **List**, **Set**, and **Delete**.
10. Válassza ki a következő **tanúsítványengedélyeket:** **Get** and **List**.
11. Válassza **az OK**gombot, majd a **Mentés**gombot.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Rendszergazdai szerepkör esetén a következő engedélyeket kell hozzáadni a Key Vaulthoz

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az `resourceGroupName`OPC-tárolót, amelyet az üzembe helyezés során használ.
3. Nyissa meg a `resourceGroupName-xxxxx`Key Vault .
4. Nyissa meg a **Hozzáférési házirendeket**.
5. Válassza **az Új hozzáadása**lehetőséget.
6. Hagyja ki a sablont. Nincs olyan sablon, amely megfelelne a követelményeknek.
7. Válassza **az Egyszerű kijelölés lehetőséget,** és jelölje ki a hozzáadni kívánt felhasználót, vagy hívjon meg egy új felhasználót a bérlőhöz.
8. Válassza ki a következő **kulcsengedélyeket:** **Get**, **List**és **Sign**.
9. Válassza ki a következő **titkos engedélyeket:** **Get**, **List**, **Set**, and **Delete**.
10. Válassza ki a következő **tanúsítványengedélyeket:** **Get**, **List**, **Update**, **Create**és **Import**.
11. Válassza **az OK**gombot, majd a **Mentés**gombot.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Felhasználói hozzáférési szabályzat eltávolítása az Azure Key Vaultból

1. Nyissa meg az Azure Portalt.
2. Nyissa meg az `resourceGroupName`OPC-tárolót, amelyet az üzembe helyezés során használ.
3. Nyissa meg a `resourceGroupName-xxxxx`Key Vault .
4. Nyissa meg a **Hozzáférési házirendeket**.
5. Keresse meg az eltávolítani kívánt felhasználót, és válassza a **Törlés gombot.**

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az OPC Vault-tanúsítványok és -felhasználók kezelését, a következőket teheti:

> [!div class="nextstepaction"]
> [Az OPC-eszközök biztonságos kommunikációja](howto-opc-vault-secure.md)
