
## keyclock define client tips


## Grafana helm values
```yaml
  grafana:
    envFromSecrets:
      - name: grafana-oauth-secret
    grafana.ini:
      auth.generic_oauth:
        enabled: true
        name: Keycloak
        allow_sign_up: false
        scopes: openid profile email roles
        auth_url: https://sso.site.info/realms/master/protocol/openid-connect/auth
        token_url: https://sso.site.info/realms/master/protocol/openid-connect/token
        api_url: https://sso.site.info/realms/master/protocol/openid-connect/userinfo
        email_attribute_path: email
        login_attribute_path: username
        name_attribute_path: full_name
        role_attribute_path: contains(roles[*], 'admin') && 'Admin' || contains(roles[*], 'editor') && 'Editor' || 'Viewer'
```

##### grafana-oauth-secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: grafana-oauth-secret
  namespace: kube-prometheus-stack
type: Opaque
stringData:
  GF_AUTH_GENERIC_OAUTH_CLIENT_ID: base64-client-id
  GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET: base64-client-secret

```
