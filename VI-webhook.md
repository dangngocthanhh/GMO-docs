# Tổng hợp Webhook API (Version 1)

**Hệ thống Webhook cho phép nhận thông báo tự động về các sự kiện giao dịch. Hiện tại, hệ thống hỗ trợ thông báo cho các giao dịch nạp tiền vào tài khoản ảo (Virtual Account)**.

* **Base URI:** `https://stg-api.gmo-aozora.com/ganb/api/webhooks/v1`
* **Xác thực (Authorization):** Sử dụng header `Authorization`. Giá trị là chuỗi `Client ID` và `Client Secret` nối với nhau bằng dấu hai chấm (`:`), sau đó mã hóa **Base64**.

** -------------------------------------------------------------------------------- **

### **1. Các Endpoint API**

#### **1.1 Kiểm soát việc gửi thông báo (Subscribe)**

**Dùng để đăng ký bắt đầu hoặc tạm dừng việc gửi thông báo từ ngân hàng.**

* **Method:** `POST /subscribe`
* **Đầu vào (Body - ****SubscribeRequestBody****):**
  * `subscribe_status` (String): Trạng thái đăng ký. `0` = Dừng gửi; `1` = Bắt đầu gửi**.**
  * `event_types` (Array): Danh sách các loại sự kiện muốn đăng ký**. Ví dụ: **`va-deposit-transaction` (Thông báo nạp tiền VA)**.**
* **Đầu ra:** Trả về thân bài trống (empty response body) nếu thành công**.**

#### **1.2 Tra cứu thông báo chưa gửi (Unsent List)**

**Lấy danh sách các thông báo nạp tiền vào tài khoản ảo đang ở trạng thái chưa gửi hoặc gửi lỗi.**

* **Method:** `GET /unsentlist/va-deposit-transaction`
* **Đặc điểm:**
  * **Chỉ áp dụng cho tài khoản ****Doanh nghiệp (Corporation)** và **Cá nhân kinh doanh (Sole Proprietor)****. Tài khoản cá nhân thông thường không được hỗ trợ**.
  * **Các thông báo đã lấy qua API này sẽ được coi là "đã gửi" và không gửi lại qua Webhook khi kích hoạt lại dịch vụ**.
* **Giới hạn:** Tối đa **500 bản ghi** mỗi lần gọi. Nếu nhận đủ 500 bản ghi, cần lặp lại yêu cầu cho đến khi nhận mã lỗi `404 Not Found` để lấy hết dữ liệu**.**
* **Đầu ra:** Đối tượng `VaDepositTransactionUnsentResponse`.

** -------------------------------------------------------------------------------- **

### **2. Cấu trúc dữ liệu chính**

#### **2.1 Thông báo giao dịch nạp tiền (**`VaDepositTransactionMessage`)

**Cấu trúc này nằm trong danh sách trả về của API Unsent List**.


| **Thuộc tính** | **Kiểu dữ liệu** | **Mô tả**                                                     |
| ---------------- | ------------------- | --------------------------------------------------------------- |
| `message_id`     | **String**          | **ID duy nhất để xác định tin nhắn**.                    |
| `timestamp`      | **String**          | **Thời gian tạo sự kiện (Định dạng ISO8601)**.           |
| `account`        | **Object**          | **Thông tin tài khoản nhận tiền chính (**`Account`)**.**  |
| `va_transaction` | **Object**          | **Chi tiết giao dịch tài khoản ảo (**`VaTransaction`)**.** |

#### **2.2 Chi tiết tài khoản nhận (**`Account`)


| **Thuộc tính**    | **Mô tả**                      |
| ------------------- | -------------------------------- |
| `ra_id`             | **ID tài khoản nhận tiền**.  |
| `ra_branch_code`    | **Mã chi nhánh ngân hàng**.  |
| `ra_account_number` | **Số tài khoản ngân hàng**. |
| `ra_holder_name`    | **Tên chủ tài khoản**.       |

#### **2.3 Chi tiết giao dịch tài khoản ảo (**`VaTransaction`)


| **Thuộc tính**     | **Mô tả**                                                                     |
| -------------------- | ------------------------------------------------------------------------------- |
| `va_id`              | **ID tài khoản ảo**.                                                         |
| `transaction_date`   | **Ngày giao dịch (YYYY-MM-DD)**.                                              |
| `va_account_number`  | **Số tài khoản ảo**.                                                        |
| `deposit_amount`     | **Số tiền nạp**.                                                             |
| `remitter_name_kana` | **Tên người chuyển khoản (Katakana)**.                                     |
| `payment_bank_name`  | **Tên ngân hàng gửi**.                                                      |
| `item_key`           | **Khóa định danh chi tiết giao dịch (timestamp tính bằng micro giây)**. |

** -------------------------------------------------------------------------------- **

#### **3. Xử lý lỗi (**`ErrorResponse`)

**Khi yêu cầu thất bại, hệ thống trả về mã lỗi và thông báo chi tiết:**

* `error_code`: Mã lỗi định dạng nửa chiều**.**
* `error_message`: Nội dung lỗi chi tiết**.**
