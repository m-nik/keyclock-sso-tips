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


## Sample AppProject
```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: frontend
  namespace: argocd
spec:
  description: "Frontend applications"
  sourceRepos:
    - 'ssh://git@gitlab.site.trade:22/front/*'
    - 'ssh://git@gitlab.site.trade:22/devops/*'
  destinations:
    - namespace: site-frontend
      server: https://kubernetes.default.svc
  clusterResourceWhitelist:
    - group: ""
      kind: Namespace
  roles:
    - name: readonly
      description: "Read-only access for frontend apps"
      policies:
        - p, proj:frontend:readonly, applications, get, frontend/*, allow
        - p, proj:frontend:readonly, applications, list, frontend/*, allow
      groups:
        - argocd-prd-frontend-readonly
    - name: admin
      description: "Full access for frontend apps"
      policies:
        - p, proj:frontend:admin, applications, *, frontend/*, allow
        - p, proj:frontend:admin, exec, create, frontend/*, allow
      groups:
        - argocd-prd-frontend-admin
```
