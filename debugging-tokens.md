
```sh
curl -X POST "https://sso.site.info/realms/master/protocol/openid-connect/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=<client-id>" \
  -d "client_secret=<client-secret>" \
  -d "grant_type=password" \
  -d "username=<your-username>" \
  -d "password=<your-password>"  | jq .access_token -r | cut -d. -f2 | base64 -d | jq

```
