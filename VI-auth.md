# Tổng hợp Quy trình Xác thực & Ủy quyền (Auth API)

Tất cả các yêu cầu API trong nhóm này đều được thực hiện thông qua Base URL: `https://stg-api.gmo-aozora.com/ganb/api/auth/v1`.

## 1. Giai đoạn 1: Lấy mã Ủy quyền (Authorization)

Đây là bước để ứng dụng (Client) nhận được sự chấp thuận từ phía người dùng.

* **Endpoint:**`GET /authorization`
* **Tham số yêu cầu (Query Parameters):**
  * `client_id`: ID của ứng dụng do ngân hàng cấp.
  * `redirect_uri`: URI để nhận mã ủy quyền sau khi người dùng chấp thuận.
  * `response_type`: Cố định là `code`.
  * `scope`: Quyền hạn yêu cầu (ví dụ: `offline_access` để lấy refresh token).
  * `state`: Chuỗi ngẫu nhiên để chống lỗi CSRF.
  * `nonce` (Tùy chọn): Chuỗi liên kết session với ID Token để chống tấn công phát lại.
* **Phản hồi thành công (**[AuthorizationResponse](docs/authorization/AuthorizationResponse.md)**):**
  * `code`: Mã ủy quyền (Authorization Code) dùng cho bước tiếp theo.
  * `state`: Trả về đúng giá trị đã gửi trong yêu cầu.

---

## 2. Giai đoạn 2: Lấy Access Token (Token)

Sử dụng mã ủy quyền có được từ Giai đoạn 1 để đổi lấy Access Token.

* **Endpoint:**`POST /token`
* **Xác thực Client (Header):**
  * `Authorization`: Sử dụng Basic Auth (Chuỗi `client_id:client_secret` được mã hóa Base64).
* **Tham số thân bài (Body - **[TokenRequest](docs/authorization/TokenRequest.md)**):**
  * **Trường hợp cấp mới:**
    * `grant_type`: Cố định là `authorization_code`.
    * `code`: Mã ủy quyền nhận được từ bước trước.
    * `redirect_uri`: Phải khớp với URI đã dùng ở bước Authorization.
  * **Trường hợp làm mới (Refresh):**
    * `grant_type`: Cố định là `refresh_token`.
    * `refresh_token`: Mã refresh token đã được cấp trước đó.
* **Phản hồi thành công (`TokenResponse`):**
  * `access_token`: Mã dùng để gửi trong header `x-access-token` của các API nghiệp vụ.
  * `refresh_token`: Dùng để lấy access token mới khi cái cũ hết hạn.
  * `expires_in`: Thời gian hết hạn của access token (tính bằng giây).
  * `id_token` (Tùy chọn): Token định danh dưới dạng JWT.

---

## 3. Xử lý lỗi (`ErrorResponse`)

Nếu yêu cầu không hợp lệ, hệ thống sẽ trả về các mã lỗi sau:


| Mã lỗi                 | Mô tả                                                      |
| :----------------------- | :----------------------------------------------------------- |
| `invalid_request`        | Tham số yêu cầu không hợp lệ hoặc thiếu.             |
| `invalid_client`         | Thông tin Client (ID/Secret) không chính xác.            |
| `invalid_grant`          | Mã ủy quyền hoặc Redirect URI không hợp lệ/hết hạn. |
| `unauthorized_client`    | Client không có quyền sử dụng`authorization_code`.      |
| `unsupported_grant_type` | Loại`grant_type`không được hỗ trợ.                    |
| `invalid_scope`          | Scope yêu cầu không hợp lệ.                             |
| `server_error`           | Lỗi phát sinh từ phía máy chủ.                         |

---

**Lưu ý:** Sau khi có được `access_token`, bạn phải sử dụng nó trong tiêu đề `x-access-token` cho mọi yêu cầu API nghiệp vụ (như chuyển khoản, truy vấn số dư).
