## keyclock define client tips


## Argocd helm values
```yaml
argo-cd:
  configs:
    cm:
      exec.enabled: true
      oidc.config: |
        name: SSO
        issuer: https://sso.site.info/realms/master
        clientID: argocd-irprd
        clientSecret: paste_clientSecret_here!!!
        requestedIDTokenClaims:
          groups:
            essential: true
        requestedScopes:
          - openid
          - groups
          - profile
          - email
    rbac:
      policy.default: ''
      # Policy rules are in the form:
      #  p, subject, resource, action, object, effect
      # Role definitions and bindings are in the form:
      #  g, subject, inherited-subject
      # policy.csv: |
      #   p, role:org-admin, applications, *, */*, allow
      #   p, role:org-admin, clusters, get, *, allow
      #   p, role:org-admin, repositories, *, *, allow
      #   p, role:org-admin, logs, get, *, allow
      #   p, role:org-admin, exec, create, */*, allow
      #   g, your-github-org:your-team, role:org-admin
      policy.csv: |
        g, argocd-prd-readonly, role:readonly
        g, argocd-prd-admin, role:admin
```
