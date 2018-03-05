Amikor egy ügyfél-tanúsítványt hoz létre, automatikusan települ azon a számítógépen, amelyet az létrehozásához használt. Ha azt szeretné, telepíti az ügyféltanúsítványt egy másik ügyfélszámítógépen, kell exportálni az ügyféltanúsítványt, ami akkor jön létre.

1. Ügyféltanúsítvány exportálásához nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Az ügyfél-tanúsítványok, ami akkor jön létre, alapértelmezés szerint találhatók "Tanúsítványok - aktuális User\Personal\Certificates". Kattintson a jobb gombbal az ügyféltanúsítvány exportálása, kattintson a kívánt **feladataival**, és kattintson a **exportálása** megnyitásához a **Tanúsítványexportáló varázsló**.

  ![Exportálás](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. A Tanúsítványexportáló varázslóban kattintson **tovább** a folytatáshoz.

  ![Következő lépés](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Válassza ki **Igen, a titkos kulcs exportálását választom**, és kattintson a **következő**.

  ![titkos kulcs exportálása](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ez a beállítás továbbá exportálja a legfelső szintű tanúsítvány adatait, a sikeres ügyfél-hitelesítés szükséges. Ennek hiányában ügyfél-hitelesítés sikertelen lesz, mivel az ügyfél nem rendelkezik a megbízható legfelső szintű tanúsítvány. Ezután kattintson a **Tovább** gombra.

  ![Exportálás fájlformátuma](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Tovább** gombra.

  ![biztonság](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.

  ![exportálandó fájl](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

  ![befejezés](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)