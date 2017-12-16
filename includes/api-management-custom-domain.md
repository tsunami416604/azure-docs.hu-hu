## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Hogyan APIM Proxy kiszolgáló válaszol, a TLS-kézfogás SSL-tanúsítványok

### <a name="clients-calling-with-sni-header"></a>Az ügyfelek SNI fejléc metódust hívja.
Az ügyfél egy vagy több egyéni tartomány Proxy konfigurálva van, APIM válaszolni tud HTTPS-kéréseket az egyéni tartomány (például contoso.com) a továbbá az alapértelmezett tartomány (például apim-szolgáltatás-name.azure-api.net). A kiszolgálónév jelzése (SNI) fejléc adatai alapján, APIM válaszol, megfelelő kiszolgálói tanúsítványt.

### <a name="clients-calling-without-sni-header"></a>Az SNI-fejléc nélküli hívó ügyfelek
Az ügyfél használja-e egy ügyfél nem küldi el a [SNI](https://tools.ietf.org/html/rfc6066#section-3) fejléc, APIM hoz létre a következő logikán alapuló válaszok:

* Ha a szolgáltatás van konfigurálva Proxy a csak egy egyéni tartományt, az alapértelmezett tanúsítvány az egyéni Proxy tartományhoz korábban kiadott tanúsítványt.
* Ha a szolgáltatás több egyéni tartomány konfigurálva proxy (csak akkor támogatott a a **prémium** réteg), az ügyfél melyik tanúsítványt kell lennie az alapértelmezett tanúsítvány is kijelölhet. Az alapértelmezett tanúsítvány beállítása a [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#definitions_hostnameconfiguration) tulajdonságot kell beállítani, igaz ("defaultSslBinding": "true"). Ha az ügyfél nem állítja be a tulajdonságot, az alapértelmezett tanúsítvány alapértelmezett Proxy tartomány a(z) *.azure-api.net kiadott tanúsítványt.
