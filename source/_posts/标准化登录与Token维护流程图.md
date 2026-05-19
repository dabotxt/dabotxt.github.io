---
title: 标准化登录与Token维护流程图
abbrlink: auth-token-flow-standard
date: 2026-05-19 16:20:00
tags:
  - 登录
  - Token
  - 安全
categories:
  - 前端工程
---

这篇文章用两张流程图讲清楚标准化登录与 Token 维护机制。

核心结论：

1. `access token` 用于业务接口鉴权，短期有效。
2. `refresh token` 用于换新 `access token`，长期有效，建议放 `HttpOnly Cookie`。
3. 前端负责维护 `Authorization: Bearer <access token>`，后端负责校验与签发。

## 图一：登录链路

{% mermaid %}
flowchart TD
  A[用户登录 /api/auth/login] --> B[后端验证凭证]
  B --> C[签发 access token]
  B --> D[Set-Cookie refreshToken HttpOnly]
  C --> E[响应 JSON token]
  E --> F[前端存内存 token]
  F --> G[设置 Authorization Bearer]
  G --> H[访问业务API]
  H --> I[后端验签通过并返回数据]
{% endmermaid %}

## 图二：刷新与重进链路

{% mermaid %}
flowchart TD
  A[业务请求返回401 access token过期] --> B[前端调用 /api/auth/refresh]
  B --> C[浏览器自动携带 refreshToken Cookie]
  C --> D[后端校验 refreshToken+session]
  D -->|通过| E[签发新 access token]
  E --> F[响应 JSON 新token]
  F --> G[前端覆盖旧token]
  G --> H[重试失败请求]

  D -->|失败| Z[跳转登录页]

  Q[关闭页面] --> R[内存token丢失]
  R --> S[重新打开页面]
  S --> T[启动时调用 /api/auth/refresh]
  T --> C
{% endmermaid %}

## 一句话理解

- Cookie 的意义：安全保存刷新凭证（`refresh token`）。
- Bearer 的意义：每次接口请求即时鉴权（`access token`）。

