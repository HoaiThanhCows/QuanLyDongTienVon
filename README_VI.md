# Quản lý dòng tiền vốn — bản khởi đầu

## 1. Tạo database
1. Mở Supabase > SQL Editor > New query.
2. Sao chép toàn bộ `supabase_setup.sql`, dán vào SQL Editor và bấm Run.
3. Vào Project Settings > API hoặc Connect, lấy Project URL và anon/public/publishable key.
4. Không đưa service_role/secret key vào HTML.

## 2. Tạo tài khoản và quyền
1. Supabase > Authentication > Users > Add user: tạo tài khoản của bạn, Quí, Vũ.
2. Sao chép UUID mỗi tài khoản.
3. Trong SQL Editor, thay các UUID trong ba câu INSERT ở cuối `supabase_setup.sql`, bỏ dấu comment `--` và chạy từng câu.
4. Bạn dùng role `manager`; Quí `investor_qui`; Vũ `investor_vu`. Không cấp manager cho nhà đầu tư.

## 3. Chạy thử
1. Mở `index.html` trên trình duyệt.
2. Nhập Project URL và public anon/publishable key, rồi đăng nhập.
3. Ghi vốn ban đầu qua 2 giao dịch: Quí 140.500.000 VNĐ; Vũ 130.500.000 VNĐ. Không thêm giao dịch 271.000.000 vì sẽ cộng trùng.
4. Ghi thử khoản chi và lần rút ví. Rút ví J&T không thay đổi số dư vốn.
5. Chọn tháng rồi bấm Xuất Excel tháng. Excel gồm 3 sheet: Tổng hợp, Sổ vốn, Rút ví J&T.

## 4. Deploy Cloudflare Pages
1. Cloudflare Dashboard > Workers & Pages > Create > Pages > Upload assets.
2. Tải lên file `index.html` hoặc thư mục chứa file rồi Deploy.
3. Mở URL được cấp, nhập Supabase URL và public key. Trên mỗi trình duyệt khác nhập cấu hình một lần; dữ liệu vẫn dùng chung nhờ Supabase.

## Ghi chú quan trọng
- Số dư = tổng vốn góp/bổ sung − chi phí.
- Rút ví Tân Nhựt/Vĩnh Lộc là sổ độc lập, không cộng vào vốn.
- RLS kiểm soát quyền ở database; kiểm thử bằng cả 3 tài khoản trước khi dùng thật.
- Đây là MVP ban đầu, chưa có giao diện sửa/xóa giao dịch; cần bổ sung quy trình điều chỉnh và nhật ký trước khi vận hành sổ tiền thật.
- Báo cáo chỉ phản ánh lịch sử đã nhập. Muốn tổng kết tháng cũ, nhập từng giao dịch theo ngày thực tế.


## 5. Đổi mật khẩu và quên mật khẩu
- Tài khoản chỉ xem (Quí/Vũ) được bật `must_change_password=true` trong `profiles` khi tạo tài khoản; sau lần đổi mật khẩu thành công, hệ thống tự tắt cờ này. Với tài khoản đã tạo từ trước, chạy một lần trong Supabase SQL Editor: `update public.profiles set must_change_password = true where role in ('investor_qui','investor_vu');`.
- Nút **Đổi mật khẩu** trong thanh tài khoản cho phép đổi mật khẩu bất cứ lúc nào bằng mật khẩu hiện tại và mật khẩu mới nhập lại.
- **Quên mật khẩu?** gửi liên kết đặt lại mật khẩu tới email thông qua Supabase Auth. Trong Supabase > Authentication > URL Configuration, cấu hình Site URL và Redirect URLs cho đúng URL đang chạy ứng dụng.
- Chạy lại toàn bộ `supabase_setup.sql` để thêm cột `must_change_password` và hàm `complete_password_change()` vào dự án hiện có. Nếu bảng `profiles` đã tồn tại, câu `ALTER TABLE ... ADD COLUMN IF NOT EXISTS` vẫn an toàn để chạy lại.
- Lưu ý: chức năng đặt lại mật khẩu yêu cầu Supabase email provider hoạt động; email có thể cần cấu hình SMTP riêng khi triển khai thực tế.
