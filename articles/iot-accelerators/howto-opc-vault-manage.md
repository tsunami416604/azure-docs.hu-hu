---
title: Az OPC Vault tanúsítványkezelő szolgáltatás kezelése – Azure | Microsoft Docs
description: Az OPC-tároló legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak és felhasználói engedélyeinek kezelése.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973804"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>Az OPC UA-tanúsítvány szolgáltatásának kezelése

Ez a cikk ismerteti az OPC UA tanúsítványkezelő szolgáltatás Azure-beli felügyeleti feladatait, a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok megújítását, a visszavont tanúsítványok listájának (CRL) megújítását, valamint a felhasználói hozzáférés engedélyezésének és visszavonásának módját.

## <a name="create-or-renew-the-root-ca-certificate"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása vagy megújítása

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához kötelező lépés az üzembe helyezés után. Érvényes kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nélkül nem lehet aláírni és kibocsátani az alkalmazás tanúsítványait.<br>Tekintse át a tanúsítványok [élettartamáról](howto-opc-vault-secure-ca.md#certificates) szóló fejezetet a tanúsítványok ésszerű és biztonságos élettartammal való kezeléséhez. A kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát az élettartamának fele után kell megújítani, de az újonnan aláírt alkalmazás-tanúsítvány beállított élettartama előtt még nem haladhatja meg a kiállítói tanúsítvány élettartamát.<br>
> [!IMPORTANT]
> A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához vagy megújításához a "rendszergazda" szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat `https://myResourceGroup-app.azurewebsites.net` , és jelentkezzen be.
2. Navigáljon a `Certificate Groups` laphoz.
3. Egy `Default` csoport van felsorolva. Kattintson a `Edit` kísérletre.
4. `Edit Certificate Group Details` A (z) szolgáltatásban módosíthatja a hitelesítésszolgáltató és az alkalmazás tanúsítványának tulajdonosi nevét és élettartamát.<br>A tulajdonos és az élettartamok csak egyszer állíthatók be az első HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány kiállítása előtt. A műveletek élettartama változása inkonzisztens élettartamot eredményezhet a kiállított tanúsítványoknak és a visszavont tanúsítványok listáinak.
5. Adjon meg egy érvényes tárgyat, például `CN=My CA Root, O=MyCompany, OU=MyDepartment`:.<br>
   > [!IMPORTANT]
   > A tulajdonos módosítása a kiállítói tanúsítvány megújításához szükséges, vagy a szolgáltatás nem fogja aláírni az alkalmazás tanúsítványait. A konfiguráció tárgya az aktív kiállítói tanúsítvány tárgya szerint van bejelölve. Ha a tárgyak nem egyeznek, a rendszer elutasítja a tanúsítvány-aláírást.
6. Kattintson a `Save` gombra.
7. Ha ezen a ponton "tiltott" hibaüzenetet kap, a felhasználói hitelesítő adatai nem rendelkeznek rendszergazdai jogosultsággal új főtanúsítvány módosításához vagy létrehozásához. Alapértelmezés szerint a szolgáltatást telepítő felhasználó rendelkezik rendszergazdai és aláírási szerepkörökkel a szolgáltatással, a többi felhasználót a AzureAD alkalmazás regisztrációjának megfelelően fel kell venni a "jóváhagyó", az "író" vagy a "rendszergazda" szerepkörbe.
8. Kattintson a `Details` gombra. A `View Certificate Group Details` a frissített információkat jeleníti meg.
9. Kattintson a `Renew CA Certificate` gombra az első kiállító hitelesítésszolgáltatói tanúsítvány kiállításához vagy a kiállítói tanúsítvány megújításához. Kattintson `Ok` a folytatáshoz.
10. Néhány másodperc elteltével a `Certificate Details` látható. A legújabb hitelesítésszolgáltatói tanúsítvány és a CRL lenyomásával `Issuer` letöltheti az OPC ua-alkalmazásait. `Crl`
11. Az OPC UA tanúsítványkezelő szolgáltatás most már készen áll az OPC UA-alkalmazások tanúsítványainak kiállítására.

## <a name="renew-the-crl"></a>A CRL megújítása

A visszavont tanúsítványok listájának (CRL) megújítása egy frissítés, amelyet rendszeres időközönként el kell osztani az alkalmazásokban. Az OPC UA-eszközök, amelyek támogatják a CRL terjesztési pont X509 bővítményét, közvetlenül frissíthetik a CRL-t a Service-végpontról. Más OPC UA-eszközökhöz manuális frissítés vagy a legjobb esetben lehet szükség, ha a GDS Server push Extensions (*) használatával frissíti a megbízhatósági listákat a tanúsítványokkal és a CRL-sel.

A következő munkafolyamatban a törölt állapotokban lévő összes tanúsítványkérelmet visszavonják a CRL-ben, amelyek megfelelnek a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványának. A CRL verziószáma eggyel nő. <br>
> [!NOTE]
> Az összes kiadott CRL érvényes a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványának lejárta előtt, mivel az OPC UA-specifikáció nem igényel kötelező, determinisztikus terjesztési modellt a CRL-hez.

> [!IMPORTANT]
> A kiállítói CRL megújításához a "rendszergazda" szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat `https://myResourceGroup.azurewebsites.net` , és jelentkezzen be.
2. Navigáljon a `Certificate Groups` laphoz.
3. Kattintson a `Details` gombra. A `View Certificate Group Details` meg kell jelennie az aktuális tanúsítványnak és a CRL-adatoknak.
4. `Update CRL Revocation List(CRL)` A gombra kattintva kiállíthatja a frissített CRL-t az OPC-tárolóban lévő összes aktív kiállító tanúsítványhoz.
5. Néhány másodperc elteltével a `Certificate Details` látható. A legújabb hitelesítésszolgáltatói tanúsítvány és a CRL lenyomásával `Issuer` letöltheti az OPC ua-alkalmazásait. `Crl`

## <a name="manage-user-roles"></a>Felhasználói szerepkörök kezelése

Az OPC-tár szolgáltatáshoz tartozó felhasználói szerepkörök kezelése a Azure Active Directory vállalati alkalmazásban történik.

A szerepkör-definíciók részletes ismertetését a [szerepkörök](howto-opc-vault-secure-ca.md#roles) szakaszban találja.

Alapértelmezés szerint a bérlőben lévő hitelesített felhasználók bejelentkezhetnek a szolgáltatásba olvasóként. Magasabb jogosultsági szintű szerepkörökhöz manuális felügyelet szükséges a Azure Portal vagy a PowerShell használatával.

### <a name="add-user"></a>Felhasználó hozzáadása

1. Nyissa meg a `portal.azure.com`Azure Portal a következő helyen:.
2. Navigáljon `Azure Active Directory` / a`Enterprise applications`következőhöz:.
3. Válassza ki az OPC Vault-szolgáltatás regisztrációját, alapértelmezés `resourceGroupName-service`szerint a.
4. Nyissa meg a `Users and Groups` címet.
5. Kattintson a `Add User` kísérletre.
6. Válassza ki vagy hívja meg a felhasználót egy adott szerepkörhöz való hozzárendeléshez.
7. Válassza ki a felhasználók szerepkörét.
8. Nyomja meg `Assign` a gombot.
9. A vagy `Administrator` `Approver` a szerepkörrel rendelkező felhasználók továbbra is hozzáadhatnak Azure Key Vault hozzáférési házirendeket.

### <a name="remove-user"></a>Felhasználó eltávolítása

1. Nyissa meg a `portal.azure.com`Azure Portal a következő helyen:.
2. Navigáljon `Azure Active Directory` / a`Enterprise applications`következőhöz:.
3. Válassza ki az OPC Vault-szolgáltatás regisztrációját, alapértelmezés `resourceGroupName-service`szerint a.
4. Nyissa meg a `Users and Groups` címet.
5. Válasszon ki egy felhasználót az eltávolítandó szerepkörrel.
6. Nyomja meg `Remove` a gombot.
7. Távolítsa el az eltávolított rendszergazdákat és jóváhagyókat Azure Key Vault szabályzatokból is.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Felhasználói hozzáférési házirend hozzáadása Azure Key Vaulthoz

A **jóváhagyóknak** és a rendszergazdáknak további hozzáférésiszabályzatokra van szükségük.

Alapértelmezés szerint a szolgáltatás identitása csak korlátozott engedélyekkel rendelkezik a Key Vaulthoz való hozzáféréshez, hogy megakadályozza a emelt szintű műveleteket vagy a módosításokat a felhasználó megszemélyesítése nélkül. Az alapszintű szolgáltatási `Get` engedélyek `List` mind a titkos kulcsok, mind a tanúsítványok esetében érvényesek. A titkok esetében csak egy kivétel létezik, a szolgáltatás a titkos `Delete` kulcsokat a felhasználó által elfogadott módon tudja tárolni. Minden egyéb művelethez a felhasználó által megszemélyesített engedélyek szükségesek.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>A **jóváhagyói szerepkörhöz** a következő engedélyeket kell hozzáadni a Key Vaulthoz:

1. Nyissa meg a `portal.azure.com`Azure Portal a következő helyen:.
2. Navigáljon az üzembe helyezés `resourceGroupName`során használt OPC-tárba.
3. Navigáljon a Key Vault `resourceGroupName-xxxxx`.
4. Navigáljon a `Access Policies`következőhöz:.
5. Kattintson a `Add new` kísérletre.
6. Hagyja ki a sablont, nincs sablon, amely megfelel a követelményeknek.
7. Kattintson a `Select Principal` elemre, és válassza ki a hozzáadni kívánt felhasználót, vagy hívja meg az új felhasználót a bérlőhöz.
8. Vizsgálat `Key permissions`: `Get` ,`List`és ami a legfontosabb. `Sign`
9. Keresés `Secret permissions`: `Get`, `List` és.`Delete` `Set`
10. Keresés `Certificate permissions`: `Get`és .`List`
11. Kattintson a `Ok` gombra.
12. `Save`változások.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>**Rendszergazdai szerepkör** esetén a következő engedélyeket kell hozzáadni a Key Vaulthoz:

1. Nyissa meg a `portal.azure.com`Azure Portal a következő helyen:.
2. Navigáljon az üzembe helyezés `resourceGroupName`során használt OPC-tárba.
3. Navigáljon a Key Vault `resourceGroupName-xxxxx`.
4. Navigáljon a `Access Policies`következőhöz:.
5. Kattintson a `Add new` kísérletre.
6. Hagyja ki a sablont, nincs sablon, amely megfelel a követelményeknek.
7. Kattintson a `Select Principal` elemre, és válassza ki a hozzáadni kívánt felhasználót, vagy hívja meg az új felhasználót a bérlőhöz.
8. Vizsgálat `Key permissions`: `Get` ,`List`és ami a legfontosabb. `Sign`
9. Keresés `Secret permissions`: `Get`, `List` és.`Delete` `Set`
10. Keresés `Certificate permissions`: `Get`, `List`,és`Import`. `Update` `Create`
11. Kattintson a `Ok` gombra.
12. `Save`változások.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Felhasználói hozzáférési házirend eltávolítása Azure Key Vault

1. Nyissa meg a `portal.azure.com`Azure Portal a következő helyen:.
2. Navigáljon az üzembe helyezés `resourceGroupName`során használt OPC-tárba.
3. Navigáljon a Key Vault `resourceGroupName-xxxxx`.
4. Navigáljon a `Access Policies`következőhöz:.
5. Keresse meg az eltávolítandó felhasználót, és kattintson `... / Delete` a be gombra a felhasználói hozzáférés törléséhez.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC-tároló tanúsítványainak és a felhasználók felügyeletét, a következő lépés a javasolt lépés:

> [!div class="nextstepaction"]
> [Az OPC-eszközök biztonságos kommunikációja](howto-opc-vault-secure.md)
