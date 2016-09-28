<properties
   pageTitle="Vállalati internetes tartomány átirányítása Traffic Manager-tartományra | Microsoft Azure"
   description="Ez a cikk segít átirányítani a vállalata tartománynevét egy Traffic Manager szolgáltatásbeli tartománynevére."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />


# Vállalati internetes tartomány átirányítása Azure Traffic Manager-tartományra

Ahhoz, hogy a vállalata tartománynevét átirányítsa egy Traffic Manager szolgáltatásbeli tartománynévre, módosítsa az internetes DNS-kiszolgálón lévő DNS-erőforrásrekordot úgy, hogy a CNAME rekordtípust használja, amely a vállalata tartománynevét leképezi a Traffic Manager-profilja tartománynevére. A Traffic Manager tartományneve a Traffic Manager-profil Konfiguráció oldalának **Általános** részében tekinthető meg.

Következzen egy példa: a www.contoso.com vállalati tartománynévnek a Traffic Manager szolgáltatásbeli tartománynévre (contoso.trafficmanager.net) történő átirányításához a DNS-erőforrásrekordot a következőre kell frissítenie:

    www.contoso.com IN CNAME contoso.trafficmanager.net

A rendszer ezentúl a *www.contoso.com* tartományhoz érkező összes forgalomkérést átirányítja a *contoso.trafficmanager.net* tartománynak.

>[AZURE.IMPORTANT] A második szintű tartomány, például a *contoso.com*, nem irányítható á Traffic Manager-tartományra. Ez a DNS-protokoll korlátozása, amely nem engedélyezi a CNAME-rekordokat a második szintű tartománynevek számára.

## Következő lépések

[A Traffic Manager útválasztási módjai](traffic-manager-routing-methods.md)

[Traffic Manager – Profil letiltása, engedélyezése vagy törlése](disable-enable-or-delete-a-profile.md)

[Traffic Manager – Végpont letiltása vagy engedélyezése](disable-or-enable-an-endpoint.md)



<!--HONumber=Sep16_HO4-->


