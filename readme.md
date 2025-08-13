# 通过 API 请求触发 Action 修改腾讯云 TEO 域名

本仓库提供一个“服务型”的 GitHub Action。你可以通过向本仓库发送一个标准的 API 请求，来触发 Action 并修改指定的腾讯云 TEO 加速域名配置。

---

## 目录
1. [准备工作 (仓库所有者)](#1-准备工作-仓库所有者)
2. [如何调用 (API请求方)](#2-如何调用-api请求方)
3. [Payload 参数详解](#3-payload-参数详解)
4. [安全提示](#4-安全提示)

---

### 1. 准备工作 (仓库所有者)

作为本仓库的所有者，你需要完成以下一次性配置：

1.  **获取腾讯云 API 密钥**：登录 [腾讯云CAM控制台](https://console.cloud.tencent.com/cam/capi) 获取 `SecretId` 和 `SecretKey`。
2.  **配置 GitHub Secrets**：在仓库的 `Settings` -> `Secrets and variables` -> `Actions` 中，添加以下两个 secrets：
    *   `TENCENTCLOUD_SECRET_ID`: 你的腾讯云 SecretId。
    *   `TENCENTCLOUD_SECRET_KEY`: 你的腾讯云 SecretKey。

完成以上步骤后，你的仓库就准备好接收 API 请求了。

---

### 2. 如何调用 (API请求方)

任何能发送 HTTP POST 请求的程序或工具，都可以通过以下方式触发本仓库的 Action。

#### 第一步：生成 GitHub Personal Access Token (PAT)

你需要一个 GitHub PAT 作为 API 请求的身份验证凭证。

1.  前往你的 GitHub **Settings** -> **Developer settings** -> **Personal access tokens** -> **Tokens (classic)**。
2.  点击 **Generate new token (classic)**。
3.  **Note** 字段填写一个描述，比如 `teo-modifier-api-token`。
4.  **Expiration** 选择一个合适的过期时间。
5.  **Select scopes** 中，**必须勾选 `repo`** (Full control of private repositories)。
6.  点击 **Generate token**，并立即复制保存好你的 token。它只会显示一次。

#### 第二步：发送 API 请求

使用 `curl` 或任何其他 HTTP 客户端，向以下地址发送一个 `POST` 请求。

**请求地址 (Endpoint):**
`https://api.github.com/repos/你的GitHub用户名/你的仓库名/dispatches`

**请求示例 (使用 `curl`):**

```bash
# 将 YOUR_GITHUB_USERNAME, YOUR_REPO_NAME, 和 YOUR_PAT 替换成你自己的信息
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_PAT" \
  "https://api.github.com/repos/YOUR_GITHUB_USERNAME/YOUR_REPO_NAME/dispatches" \
  -d '{
    "event_type": "modify-teo-domain",
    "client_payload": {
      "zone_id": "zone-225qgrnvbi9w",
      "domain_name": "qq.com",
      "origin_server": "origin.qq.com",
      "https_origin_port": 8443,
      "ipv6_status": "on"
    }
  }'