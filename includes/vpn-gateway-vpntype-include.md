* **Házirendalapú VPN-típus:** A házirendalapú VPN korábbi megnevezése statikus útválasztó átjáró volt a klasszikus üzembe helyezési modellben. A házirendalapú VPN-ek a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációkkal konfigurált IPsec-házirendek alapján titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában. A házirendalapú VPN-típus értéke *PolicyBased*.
* **Útvonalalapú VPN-típus**: Az útvonalalapú VPN korábbi megnevezése dinamikus útválasztó átjáró volt a klasszikus üzembe helyezési modellben. Az útvonalalapú VPN-ek „útvonalakat” használnak az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. Az útvonalalapú VPN-ek házirendje (vagy forgalomválasztója) bármely két elem közöttiként (vagy helyettesítő karakterekként) van konfigurálva. Az útvonalalapú VPN-típus értéke *RouteBased*.

<!--HONumber=Jun16_HO2-->


