# Installing new certs
1. Get the cert from PositiveSSL. Follow steps to generate a new key and CSR.
2. Download the certificates
3. Concatanate the `madison-data_house.cert` on top of the bundle.
4. The cert will need to be installed into both nginx certs directory and the pgadmin certs directory.
5. The pgadmin certs will need to be renamed to `server.cert`. The pgadmin certs will also need to be owned by the 5050 user in the 5050 group. `sudo chown 5050:5050 -R .` from the certs directory.
