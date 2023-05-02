Onboarding Checklist

### Common Hints

See [Certificate Governance](concepts_certificate_governance.html)

**Note:** In the embedded image the following relabels apply:
* DCCG -> TNG
* CSCA -> SCA
* DCC -> GDHCN
* NB -> TNP


It is highly recommended:
- **to use certificates issued from a public CA which follows the CAB Forum Rules**
- **not to reuse any certificates across the different staging environments**

### Links to the Environments

- Acceptance Environment: https://tng-uat.who.int

### Test Environment

For a successfull connection to the gateway there are several steps to prepare: 

 1) Certificates must be prepared for Test Environment (self signed allowed)
    - Authentication: TNP<sub>TLS</sub>
    - Upload:   TNP<sub>UP</sub>
    - SCA(s):  TNP<sub>SCA</sub>
 2) Send the Public Keys in PEM Format to the contact of the Test Operator (tng-support@who.int functional mailbox)
 3) After Onboarding in the Test Environment, check the connectivity with the following command:<br>
  ```curl -vvv -H "Accept: */*" --resolve ****.ec.europa.eu:443 --cert "auth_de.pem" --key "key.pem" https://****.ec.europa.eu/trustList``` <br>
    You should see a output like: <br>
    {% include img.html img="TrustListResult.PNG" caption="Trust List Output" width="70%" %}

 4) Test the other Truslist Routes in the same style (e.g. with DSC/SCA/Upload/Authentication...)
 5) Create an Document Signer Certificate and sign it by the SCA
 6) Create an CMS Package with the following Command: 
  ``` 
      openssl x509 -outform der -in cert.pem -out cert.der
      openssl cms -sign -nodetach -in cert.der -signer signing.crt -inkey signing.key -out signed.der -outform DER -binary
      openssl base64 -in signed.der -out cms.b64 -e -A 
  ``` 
   Note: cert.der is your DSC, signing.crt ist the Uploader Certificate)
  
 7) Upload the CMS Package to the Gateway<br>
    ```curl -v -X POST -H "Content-Type: application/cms" --cert auth_de.pem --key key.pem --data @cms.b64 https://****.ec.europa.eu/signerCertificate``` <br>
 8) Download the Trustlist again, and check if your DSC is available.
 
 
**Note**: Some versions of curl don't attach the client certificates automatically. This can be checked via
``` curl --version ```
Ensure that the used version is linked to OpenSSL. Especially under Windows (https://curl.se/windows/): 
<br><br>
OpenSSL Test Example (working)<br>
<br>
{% include img.html img="OpenSSL.PNG" caption="Working Setup" width="70%" %}
<br><br>
WinSSL Test Example (Not working)
<br><br>
{% include img.html img="WinSSL.PNG" caption="Non Working Setup" width="70%" %}



### Acceptance Environment

1) Order/Generate your certificates according the defined requirements in [Certificate Governance](concepts_certificate_governace.md):
    - Authentication: TNP<sub>TLS</sub> 
    - Upload:   TNP<sub>UP</sub>
    - SCA(s):  TNP<sub>SCA</sub> 
2) Transfer the certificates via circABC to the Secretariat
3) After the certificate whitelisting, test the functionality again with your backend (E2E)


### Production Environment

1) Push the certificates together with your application for going live to the TNG secretariat (TBD) 
2) Connect your production setup



    
