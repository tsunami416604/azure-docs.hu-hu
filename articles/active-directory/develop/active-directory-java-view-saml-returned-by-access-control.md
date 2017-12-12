---
title: "A hozzáférés-vezérlési szolgáltatásban (Java) által visszaadott SAML megtekintése"
description: "Útmutató: a hozzáférés-vezérlési szolgáltatásban az Azure-platformon futó Java-alkalmazások által visszaadott SAML megtekintése."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mtillman
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: d239145806be19d2199314fa351d1121f52203c8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>A Azure Access Control szolgáltatás által visszaadott SAML megtekintése
Ez az útmutató bemutatja, hogyan kell megtekinteni az alapul szolgáló Security Assertion Markup Language (SAML) adott vissza az alkalmazás az Azure Access Control Service (ACS). Az útmutatóban épít, a [webes felhasználók hitelesítéséhez az Azure Access Control Service használatával Eclipse hogyan](active-directory-java-authenticate-users-access-control-eclipse.md) témakör, adja meg a kódot, amely az SAML-információkat jeleníti meg. A kész alkalmazás az alábbihoz hasonlóan fog kinézni.

![Példa SAML kimenet][saml_output]

Az ACS további információkért lásd: a [további lépések](#next_steps) szakasz.

> [!NOTE]
> Az Azure Access Services vezérlő szűrő egy közösségi technológiai előzetes. Kiadás előtti szoftverként azt van hivatalosan Microsoft nem támogatja.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feladatok végrehajtása, végezze el a minta a [webes felhasználók hitelesítéséhez az Azure Access Control Service használatával Eclipse hogyan](active-directory-java-authenticate-users-access-control-eclipse.md) használják a kiindulási pont, ehhez az oktatóanyaghoz.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>A build elérési útját és a központi telepítés szerelvényre a JspWriter könyvtár hozzáadása
Adja hozzá a könyvtárban, amely tartalmazza a **javax.servlet.jsp.JspWriter** osztály a build elérési útját és a központi telepítés szerelvényre. Ha Tomcat használ, a könyvtárban van **jsp-api.jar**, az Apache található **lib** mappát.

1. A Project Explorer Eclipse meg, kattintson a jobb gombbal **MyACSHelloWorld**, kattintson **Build elérési**, kattintson **Build elérési konfigurálása**, kattintson a **szalagtárak** fülre, majd **külső JARs hozzáadása**.
2. Az a **JAR kijelölés** párbeszédpanelen keresse meg a szükséges JAR, válassza ki azt, és kattintson **nyitott**.
3. Az a **MyACSHelloWorld tulajdonságainak** párbeszédpanelen megnyitva, kattintson **telepítési szerelvény**.
4. A a **webalkalmazás telepítési szerelvény** párbeszédpanel, kattintson a **hozzáadása**.
5. Az a **új szerelvény irányelv** párbeszédpanel, kattintson a **Java Build elérési bejegyzések** , majd **következő**.
6. Válassza ki a megfelelő könyvtárban, és kattintson a **Befejezés**.
7. Kattintson a **OK** bezárásához a **MyACSHelloWorld tulajdonságainak** párbeszédpanel.

## <a name="modify-the-jsp-file-to-display-saml"></a>A SAML megjelenítendő JSP-fájl módosítása
Módosítsa **index.jsp** az alábbi kód használatával.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Az alkalmazás futtatása
1. Futtassa az alkalmazást a számítógép-emulátorban, vagy telepítse az Azure-bA leírt lépéseket [webes felhasználók hitelesítéséhez az Azure Access Control Service használatával Eclipse hogyan](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Elindít egy böngészőt, és nyissa meg a webes alkalmazás. A bejelentkezés után az alkalmazás, látni fogja a SAML információt, beleértve az identitásszolgáltató által biztosított biztonsági helyességi feltételt.

## <a name="next-steps"></a>Következő lépések
További megismerkedhet az ACS azon funkcióit és kifinomultabb forgatókönyvek kísérletezhet, olvassa el [Access Control Service 2.0][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
