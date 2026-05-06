# **Tổng hợp API GMO Aozora Business Banking**

**Tất cả các API dưới đây đều yêu cầu tiêu đề HTTP chứa `x_access_token` để xác thực.**

### **1. Nhóm API Tài khoản (Account API)**

**Nhóm này dùng để truy vấn thông tin và lịch sử của các tài khoản doanh nghiệp.**

| **Tên API / Endpoint** | **Tác dụng** | **Đầu vào chính (Input)** | **Đầu ra (Return Type)** |
| ---------------------- | ------------ | ------------------------- | ------------------------ |
| **Danh sách tài khoản** `GET /accounts` | **Truy vấn danh sách tất cả các tài khoản đang sở hữu**. | `x_access_token` | **[AccountsResponse](docs/corporation/AccountsResponse.md)** |
| **Truy vấn số dư** `GET /accounts/balances` | **Kiểm tra số dư của một hoặc tất cả tài khoản**. | `account_id` (tùy chọn) | **[BalancesResponse](docs/corporation/BalancesResponse.md)** |
| **Chi tiết vào/ra** `GET /accounts/transactions` | **Tra cứu lịch sử nạp/rút tiền của tài khoản**. | `account_id`, `date_from`, `date_to` | **[TransactionsResponse](docs/corporation/TransactionsResponse.md)** |
| **Chi tiết tiền nạp** `GET /accounts/deposit-transactions` | **Tra cứu chi tiết các giao dịch chuyển khoản đến**. | `account_id`, `date_from`, `date_to` | **[DepositTransactionsResponse](docs/corporation/DepositTransactionsResponse.md)** |
| **Giao dịch Visa Debit** `GET /accounts/visa-transactions` | **Tra cứu lịch sử giao dịch thẻ Visa Debit**. | `account_id`, `date_from`, `date_to` | **[VisaTransactionsResponse](docs/corporation/VisaTransactionsResponse.md)** |

### **2. Nhóm API Chuyển khoản (Transfer API)**

**Dùng cho các lệnh chuyển tiền đơn lẻ hoặc theo nhóm (tối đa 99 lệnh/yêu cầu).**

| **Tên API / Endpoint** | **Tác dụng** | **Đầu vào chính (Input)** | **Đầu ra (Return Type)** |
| ---------------------- | ------------ | ------------------------- | ------------------------ |
| **Yêu cầu chuyển khoản** `POST /transfer/request` | **Thực hiện chuyển khoản ngay hoặc đặt lịch**. | `body`: **[TransferRequest](docs/corporation/TransferRequest.md)** | **[TransferRequestResponse](docs/corporation/TransferRequestResponse.md)** |
| **Kiểm tra phí** `POST /transfer/transferfee` | **Kiểm tra phí và tính hợp lệ trước khi chuyển**. | `body`: **[TransferRequest](docs/corporation/TransferRequest.md)** | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)** |
| **Kết quả yêu cầu** `GET /transfer/request-result` | **Kiểm tra xem yêu cầu đã được tiếp nhận chưa**. | `apply_no` | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái giao dịch** `GET /transfer/status` | **Theo dõi tiến độ chi tiết của lệnh chuyển tiền**. | `account_id`, `query_key_class` | **[TransferStatusResponse](docs/corporation/TransferStatusResponse.md)** |
| **Hủy lệnh** `POST /transfer/cancel` | **Hủy các lệnh đang chờ hoặc đã đặt lịch**. | `body`: **[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)** | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)** |

### **3. Nhóm API Chuyển khoản theo lô (Bulk Transfer API)**

**Dùng cho các giao dịch thanh toán quy mô lớn.**

| **Tên API / Endpoint** | **Tác dụng** | **Đầu vào chính (Input)** | **Đầu ra (Return Type)** |
| ---------------------- | ------------ | ------------------------- | ------------------------ |
| **Yêu cầu chuyển lô** `POST /bulktransfer/request` | **Gửi yêu cầu chuyển khoản tổng hợp**. | `body`: **[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)** | **[BulkTransferRequestResponse](docs/corporation/BulkTransferRequestResponse.md)** |
| **Kiểm tra phí lô** `POST /bulktransfer/transferfee` | **Kiểm tra phí cho danh sách chuyển khoản lô**. | `body`: **[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)** | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)** |
| **Kết quả yêu cầu lô** `GET /bulktransfer/request-result` | **Kiểm tra trạng thái tiếp nhận của lô tiền**. | `apply_no` | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái lô** `GET /bulktransfer/status` | **Theo dõi tiến độ và lịch sử chuyển khoản lô**. | `account_id`, `query_key_class` | **[BulkTransferStatusResponse](docs/corporation/BulkTransferStatusResponse.md)** |
| **Hủy lệnh lô** `POST /bulktransfer/cancel` | **Hủy yêu cầu chuyển khoản lô**. | `body`: **[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)** | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)** |

### **4. Nhóm API Tài khoản ảo (Virtual Account API)**

**Quản lý các tài khoản định danh để đối soát nạp tiền tự động.**

| **Tên API / Endpoint** | **Tác dụng** | **Đầu vào chính (Input)** | **Đầu ra (Return Type)** |
| ---------------------- | ------------ | ------------------------- | ------------------------ |
| **Phát hành VA** `POST /va/issue` | **Tạo mới tài khoản ảo (tối đa 1.000/lần)**. | `body`: **[VaIssueRequest](docs/corporation/VaIssueRequest.md)** (tùy chọn) | **[VaIssueResponse](docs/corporation/VaIssueResponse.md)** |
| **Danh sách VA** `POST /va/list` | **Tra cứu danh sách tài khoản ảo đã phát hành**. | `body`: **[VaListRequest](docs/corporation/VaListRequest.md)** (tùy chọn) | **[VaListResponse](docs/corporation/VaListResponse.md)** |
| **Nạp tiền vào VA** `GET /va/deposit-transactions` | **Tra cứu các giao dịch khách nạp tiền vào VA**. | `ra_id` hoặc `va_id`, `date_range` | **[VaDepositTransactionsResponse](docs/corporation/VaDepositTransactionsResponse.md)** |
| **Đổi trạng thái** `POST /va/status-change` | **Tạm dừng, kích hoạt lại hoặc xóa VA**. | `body`: **[VaStatusChangeRequest](docs/corporation/VaStatusChangeRequest.md)** | **[VaStatusChangeResponse](docs/corporation/VaStatusChangeResponse.md)** |
| **Hủy hợp đồng VA** `POST /va/close-request` | **Đăng ký chấm dứt dịch vụ tài khoản ảo**. | `body`: **[VaCloseRequest](docs/corporation/VaCloseRequest.md)** | **[VaCloseRequestResponse](docs/corporation/VaCloseRequestResponse.md)** |

| **Tên API / Endpoint**                                | **Tác dụng**                                               | **Đầu vào chính (Input)**     | **Đầu ra (Return Type)**        |
| ------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | --------------------------------- |
| **Yêu cầu chuyển khoản** `POST /transfer/request`  | **Thực hiện chuyển khoản ngay hoặc đặt lịch**.       | `body`:**[TransferRequest](docs/corporation/TransferRequest.md)**.       | **[TransferRequestResponse](docs/corporation/TransferRequestResponse.md)**       |
| **Kiểm tra phí** `POST /transfer/transferfee`        | **Kiểm tra phí và tính hợp lệ trước khi chuyển**.   | `body`:**[TransferRequest](docs/corporation/TransferRequest.md)**.       | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)**           |
| **Kết quả yêu cầu** `GET /transfer/request-result` | **Kiểm tra xem yêu cầu đã được tiếp nhận chưa**.  | `apply_no`(số tiếp nhận)**.**  | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái giao dịch** `GET /transfer/status`     | **Theo dõi tiến độ chi tiết của lệnh chuyển tiền**. | `account_id`,`query_key_class`.   | **[TransferStatusResponse](docs/corporation/TransferStatusResponse.md)**        |
| **Hủy lệnh** `POST /transfer/cancel`                 | **Hủy các lệnh đang chờ hoặc đã đặt lịch**.       | `body`:**[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)**. | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)**        |

### **3. Nhóm API Chuyển khoản theo lô (Bulk Transfer API)**

**Dùng cho các giao dịch thanh toán quy mô lớn**.


| **Tên API / Endpoint**                                        | **Tác dụng**                                             | **Đầu vào chính (Input)**     | **Đầu ra (Return Type)**        |
| -------------------------------------------------------------- | ---------------------------------------------------------- | --------------------------------- | --------------------------------- |
| **Yêu cầu chuyển lô** `POST /bulktransfer/request`         | **Gửi yêu cầu chuyển khoản tổng hợp**.              | `body`:**[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)**.   | **[BulkTransferRequestResponse](docs/corporation/BulkTransferRequestResponse.md)**   |
| **Kiểm tra phí lô** `POST /bulktransfer/transferfee`        | **Kiểm tra phí cho danh sách chuyển khoản lô**.      | `body`:**[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)**.   | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)**           |
| **Kết quả yêu cầu lô** `GET /bulktransfer/request-result` | **Kiểm tra trạng thái tiếp nhận của lô tiền**.     | `apply_no`.                       | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái lô** `GET /bulktransfer/status`                | **Theo dõi tiến độ và lịch sử chuyển khoản lô**. | `account_id`,`query_key_class`.   | **[BulkTransferStatusResponse](docs/corporation/BulkTransferStatusResponse.md)**    |
| **Hủy lệnh lô** `POST /bulktransfer/cancel`                 | **Hủy yêu cầu chuyển khoản lô**.                     | `body`:**[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)**. | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)**        |

### **4. Nhóm API Tài khoản ảo (Virtual Account API)**

**Quản lý các tài khoản định danh để đối soát nạp tiền tự động**.


| **Tên API / Endpoint**                               | **Tác dụng**                                            | **Đầu vào chính (Input)**              | **Đầu ra (Return Type)**        |
| ----------------------------------------------------- | --------------------------------------------------------- | ------------------------------------------ | --------------------------------- |
| **Phát hành VA** `POST /va/issue`                   | **Tạo mới tài khoản ảo (tối đa 1.000/lần)**.      | `body`:**[VaIssueRequest](docs/corporation/VaIssueRequest.md)**(tùy chọn)**.** | **[VaIssueResponse](docs/corporation/VaIssueResponse.md)**               |
| **Danh sách VA** `POST /va/list`                     | **Tra cứu danh sách tài khoản ảo đã phát hành**. | `body`:**[VaListRequest](docs/corporation/VaListRequest.md)**(tùy chọn)**.**  | **[VaListResponse](docs/corporation/VaListResponse.md)**                |
| **Nạp tiền vào VA** `GET /va/deposit-transactions` | **Tra cứu các giao dịch khách nạp tiền vào VA**.   | `ra_id`hoặc`va_id`,`date_range`.          | **[VaDepositTransactionsResponse](docs/corporation/VaDepositTransactionsResponse.md)** |
| **Đổi trạng thái** `POST /va/status-change`       | **Tạm dừng, kích hoạt lại hoặc xóa VA**.           | `body`:**[VaStatusChangeRequest](docs/corporation/VaStatusChangeRequest.md)**.          | **[VaStatusChangeResponse](docs/corporation/VaStatusChangeResponse.md)**        |
| **Hủy hợp đồng VA** `POST /va/close-request`      | **Đăng ký chấm dứt dịch vụ tài khoản ảo**.      | `body`:**[VaCloseRequest](docs/corporation/VaCloseRequest.md)**.                 | **[VaCloseRequestResponse](docs/corporation/VaCloseRequestResponse.md)**        |

**Tất cả các API dưới đây đều yêu cầu tiêu đề HTTP chứa **`x_access_token` để xác thực**.**

### **1. Nhóm API Tài khoản (Account API)**

**Nhóm này dùng để truy vấn thông tin và lịch sử của các tài khoản doanh nghiệp.**


| **Tên API / Endpoint**                                       | **Tác dụng**                                                     | **Đầu vào chính (Input)**       | **Đầu ra (Return Type)**      |
| ------------------------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------- | ------------------------------- |
| **Danh sách tài khoản** `GET /accounts`                    | **Truy vấn danh sách tất cả các tài khoản đang sở hữu**. | `x_access_token`.                   | **[AccountsResponse](docs/corporation/AccountsResponse.md)**            |
| **Truy vấn số dư** `GET /accounts/balances`                | **Kiểm tra số dư của một hoặc tất cả tài khoản**.        | `account_id`(tùy chọn)**.**       | **[BalancesResponse](docs/corporation/BalancesResponse.md)**            |
| **Chi tiết vào/ra** `GET /accounts/transactions`            | **Tra cứu lịch sử nạp/rút tiền của tài khoản**.           | `account_id`,`date_from`,`date_to`. | **[TransactionsResponse](docs/corporation/TransactionsResponse.md)**        |
| **Chi tiết tiền nạp** `GET /accounts/deposit-transactions` | **Tra cứu chi tiết các giao dịch chuyển khoản đến**.       | `account_id`,`date_from`,`date_to`. | **[DepositTransactionsResponse](docs/corporation/DepositTransactionsResponse.md)** |
|                                                               |                                                                    |                                     |                                 |
| **Giao dịch Visa Debit** `GET /accounts/visa-transactions`   | **Tra cứu lịch sử giao dịch thẻ Visa Debit**.                 | `account_id`,`date_from`,`date_to`. | **[VisaTransactionsResponse](docs/corporation/VisaTransactionsResponse.md)**    |

### **2. Nhóm API Chuyển khoản (Transfer API)**

**Dùng cho các lệnh chuyển tiền đơn lẻ hoặc theo nhóm (tối đa 99 lệnh/yêu cầu)**.


| **Tên API / Endpoint**                                | **Tác dụng**                                               | **Đầu vào chính (Input)**     | **Đầu ra (Return Type)**        |
| ------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | --------------------------------- |
| **Yêu cầu chuyển khoản** `POST /transfer/request`  | **Thực hiện chuyển khoản ngay hoặc đặt lịch**.       | `body`:**[TransferRequest](docs/corporation/TransferRequest.md)**.       | **[TransferRequestResponse](docs/corporation/TransferRequestResponse.md)**       |
| **Kiểm tra phí** `POST /transfer/transferfee`        | **Kiểm tra phí và tính hợp lệ trước khi chuyển**.   | `body`:**[TransferRequest](docs/corporation/TransferRequest.md)**.       | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)**           |
| **Kết quả yêu cầu** `GET /transfer/request-result` | **Kiểm tra xem yêu cầu đã được tiếp nhận chưa**.  | `apply_no`(số tiếp nhận)**.**  | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái giao dịch** `GET /transfer/status`     | **Theo dõi tiến độ chi tiết của lệnh chuyển tiền**. | `account_id`,`query_key_class`.   | **[TransferStatusResponse](docs/corporation/TransferStatusResponse.md)**        |
| **Hủy lệnh** `POST /transfer/cancel`                 | **Hủy các lệnh đang chờ hoặc đã đặt lịch**.       | `body`:**[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)**. | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)**        |

### **3. Nhóm API Chuyển khoản theo lô (Bulk Transfer API)**

**Dùng cho các giao dịch thanh toán quy mô lớn**.


| **Tên API / Endpoint**                                        | **Tác dụng**                                             | **Đầu vào chính (Input)**     | **Đầu ra (Return Type)**        |
| -------------------------------------------------------------- | ---------------------------------------------------------- | --------------------------------- | --------------------------------- |
| **Yêu cầu chuyển lô** `POST /bulktransfer/request`         | **Gửi yêu cầu chuyển khoản tổng hợp**.              | `body`:**[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)**.   | **[BulkTransferRequestResponse](docs/corporation/BulkTransferRequestResponse.md)**   |
| **Kiểm tra phí lô** `POST /bulktransfer/transferfee`        | **Kiểm tra phí cho danh sách chuyển khoản lô**.      | `body`:**[BulkTransferRequest](docs/corporation/BulkTransferRequest.md)**.   | **[TransferFeeResponse](docs/corporation/TransferFeeResponse.md)**           |
| **Kết quả yêu cầu lô** `GET /bulktransfer/request-result` | **Kiểm tra trạng thái tiếp nhận của lô tiền**.     | `apply_no`.                       | **[TransferRequestResultResponse](docs/corporation/TransferRequestResultResponse.md)** |
| **Trạng thái lô** `GET /bulktransfer/status`                | **Theo dõi tiến độ và lịch sử chuyển khoản lô**. | `account_id`,`query_key_class`.   | **[BulkTransferStatusResponse](docs/corporation/BulkTransferStatusResponse.md)**    |
| **Hủy lệnh lô** `POST /bulktransfer/cancel`                 | **Hủy yêu cầu chuyển khoản lô**.                     | `body`:**[TransferCancelRequest](docs/corporation/TransferCancelRequest.md)**. | **[TransferCancelResponse](docs/corporation/TransferCancelResponse.md)**        |

### **4. Nhóm API Tài khoản ảo (Virtual Account API)**

**Quản lý các tài khoản định danh để đối soát nạp tiền tự động**.


| **Tên API / Endpoint**                               | **Tác dụng**                                            | **Đầu vào chính (Input)**              | **Đầu ra (Return Type)**        |
| ----------------------------------------------------- | --------------------------------------------------------- | ------------------------------------------ | --------------------------------- |
| **Phát hành VA** `POST /va/issue`                   | **Tạo mới tài khoản ảo (tối đa 1.000/lần)**.      | `body`:**[VaIssueRequest](docs/corporation/VaIssueRequest.md)**(tùy chọn)**.** | **[VaIssueResponse](docs/corporation/VaIssueResponse.md)**               |
| **Danh sách VA** `POST /va/list`                     | **Tra cứu danh sách tài khoản ảo đã phát hành**. | `body`:**[VaListRequest](docs/corporation/VaListRequest.md)**(tùy chọn)**.**  | **[VaListResponse](docs/corporation/VaListResponse.md)**                |
| **Nạp tiền vào VA** `GET /va/deposit-transactions` | **Tra cứu các giao dịch khách nạp tiền vào VA**.   | `ra_id`hoặc`va_id`,`date_range`.          | **[VaDepositTransactionsResponse](docs/corporation/VaDepositTransactionsResponse.md)** |
| **Đổi trạng thái** `POST /va/status-change`       | **Tạm dừng, kích hoạt lại hoặc xóa VA**.           | `body`:**[VaStatusChangeRequest](docs/corporation/VaStatusChangeRequest.md)**.          | **[VaStatusChangeResponse](docs/corporation/VaStatusChangeResponse.md)**        |
| **Hủy hợp đồng VA** `POST /va/close-request`      | **Đăng ký chấm dứt dịch vụ tài khoản ảo**.      | `body`:**[VaCloseRequest](docs/corporation/VaCloseRequest.md)**.                 | **[VaCloseRequestResponse](docs/corporation/VaCloseRequestResponse.md)**        |
