
## keyclock grafana client configuration
https://grafana.com/docs/grafana/latest/setup-grafana/configure-security/configure-authentication/keycloak/
- Client ID: grafana-oauth
- Enabled: ON
- Client Protocol: openid-connect
- Client authentication  ON (Access Type: confidential)
- Standard Flow Enabled: ON
- Implicit Flow Enabled: OFF
- Direct Access Grants Enabled: ON
- Root URL: <grafana_root_url>
- Valid Redirect URIs: <grafana_root_url>/login/generic_oauth
- Web Origins: <grafana_root_url>
- Admin URL: <grafana_root_url>
- Base URL: <grafana_root_url>
- Client scopes:
  - email
  - offline_access
  - profile
  - roles
## Grafana helm values
```yaml
  grafana:
    envFromSecrets:
      - name: grafana-oauth-secret
    grafana.ini:
      server:
        root_url: https://grafana.site.info
      #log:
      #  level: debug
      auth.generic_oauth:
        enabled: true
        name: Keycloak
        allow_sign_up: true
        scopes: openid profile email roles
        auth_url: https://sso.site.info/realms/master/protocol/openid-connect/auth
        token_url: https://sso.site.info/realms/master/protocol/openid-connect/token
        api_url: https://sso.site.info/realms/master/protocol/openid-connect/userinfo
        email_attribute_path: email
        login_attribute_path: preferred_username
        name_attribute_path: name
        # bug with using roles. grafana don't get roles in auth!
        # role_attribute_path: contains(realm_access.roles[*], 'grafana_admin') && 'Admin' || contains(realm_access.roles[*], 'grafana_editor') && 'Editor' || 'Viewer'
        role_attribute_path: >
          contains(groups, 'Grafana-prd-admin') && 'Admin' ||
          contains(groups, 'Grafana-prd-editor') && 'Editor' ||
          contains(groups, 'Grafana-prd-viewer') && 'Viewer'
        role_attribute_strict: true  # prevents login without any role
        allow_assign_grafana_admin: true
```

##### grafana-oauth-secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: grafana-oauth-secret
  namespace: kube-prometheus-stack
type: Opaque
data:
  GF_AUTH_GENERIC_OAUTH_CLIENT_ID: base64-client-id
  GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET: base64-client-secret

```



## Debugging
1. enable grafana debug mode
```yaml 
  grafana.ini:
    log:
      level: debug
```
2. https://jmespath.org to test `role_attribute_path`
