# Tự học

Ứng dụng tự học và kiểm tra kiến thức dạng Single-Page tĩnh (Jamstack). Không cần server, không cần build tool — mở thẳng trình duyệt là chạy.

---

## Cấu trúc thư mục

```
/
├── index.html            # Layout + toàn bộ logic JavaScript
├── style.css             # Design system Minimalist Monochrome
├── danh-sach-de.json     # Manifest: danh sách môn học & đề thi
├── de-hoa-so-1.json      # Dữ liệu đề thi mẫu
└── README.md
```

## Trang chủ / Dashboard

Truy cập `index.html` (không có `?de=`) để vào trang **Kho Đề Thi**. Dashboard tự động:
- Đọc `danh-sach-de.json` để lấy danh sách môn học và đề thi.
- Quét `localStorage` của từng đề, hiển thị thanh tiến độ và nhãn trạng thái (`CHƯA LÀM / ĐANG LÀM / HOÀN THÀNH`) ngay trên card — không cần fetch file JSON đề thi.
- Click vào card → chuyển sang `?de=<id>` để làm bài.

## Thêm môn học / đề thi mới

Mở `danh-sach-de.json` và chỉnh sửa:

---

## Cách dùng

### Chạy local

Do dùng `fetch()` để tải JSON, trình duyệt sẽ chặn nếu mở file trực tiếp (`file://`). Cần chạy qua một HTTP server nhỏ:

```bash
# Python 3
python -m http.server 8080

# Node.js (npx)
npx serve .
```

Sau đó truy cập:

```
http://localhost:8080/index.html?de=de-hoa-so-1
```

Tham số `?de=` là tên file JSON (không có đuôi `.json`).

### Deploy lên GitHub Pages

1. Push toàn bộ thư mục lên một GitHub repo.
2. Vào **Settings → Pages**, chọn branch `main`, thư mục `/root`.
3. Truy cập theo URL dạng:

```
https://<username>.github.io/<repo>/?de=de-hoa-so-1
```

### Dùng file JSON từ raw GitHub

Mở `index.html`, tìm dòng:

```js
const BASE_URL = "./";
```

Đổi thành raw URL của thư mục chứa file JSON trên GitHub:

```js
const BASE_URL = "https://raw.githubusercontent.com/<user>/<repo>/main/";
```

---

## Thêm môn học / đề thi mới

Mở `danh-sach-de.json` — đây là nơi duy nhất cần chỉnh để thêm đề:

```json
[
  {
    "mon": "Hóa Học",
    "mon_id": "hoa-hoc",
    "mo_ta": "Mô tả ngắn về môn",
    "de_thi": [
      {
        "id": "de-hoa-so-1",
        "ten": "Đề số 1",
        "mo_ta": "Trắc nghiệm tổng hợp — Học kỳ 2",
        "tong_cau": 28
      }
    ]
  }
]
```

Trường `tong_cau` dùng để tính % tiến độ trên Dashboard mà không cần fetch file đề thi. Giữ đồng bộ với số câu thực tế. Môn chưa có đề thì để `"de_thi": []`.

Sau đó tạo file `<id>.json` tương ứng trong cùng thư mục.

### Schema JSON câu hỏi

Mỗi phần tử trong mảng là một câu hỏi. Ba loại được hỗ trợ:

**Phần I — Trắc nghiệm 4 lựa chọn**

```json
{
  "phan": "I",
  "loai": "trac_nghiem_4_lua_chon",
  "so_cau": 1,
  "cau_hoi": "Nội dung câu hỏi, hỗ trợ $LaTeX$",
  "lua_chon": { "A": "...", "B": "...", "C": "...", "D": "..." },
  "dap_an_dung": "A",
  "co_hinh_anh": false,
  "huong_dan_giai": "Giải thích (tuỳ chọn)"
}
```

**Phần II — Trắc nghiệm Đúng/Sai**

```json
{
  "phan": "II",
  "loai": "trac_nghiem_dung_sai",
  "so_cau": 1,
  "cau_hoi": "Đoạn dẫn câu hỏi",
  "y_nho": [
    {
      "ky_hieu": "a",
      "noi_dung": "Nội dung ý nhỏ",
      "ket_qua": "Dung",
      "giai_thich": "Giải thích tại sao"
    }
  ],
  "huong_dan_giai": "Tuỳ chọn"
}
```

`ket_qua` nhận giá trị `"Dung"` hoặc `"Sai"`.

**Phần III — Trả lời ngắn**

```json
{
  "phan": "III",
  "loai": "tra_loi_ngan",
  "so_cau": 1,
  "cau_hoi": "Nội dung câu hỏi",
  "dap_an_dung": "304",
  "huong_dan_giai": "Tuỳ chọn, hỗ trợ $LaTeX$"
}
```

---

## Tính năng

| Tính năng | Chi tiết |
|---|---|
| **Công thức LaTeX** | MathJax 3 qua CDN, inline `$ $` và display `$$ $$` |
| **Lưu tiến độ** | `localStorage` theo key gắn với tên đề, phục hồi khi F5 |
| **Sticky ribbon** | Hiển thị mã đề + `PROGRESS: X / Y (Z%)` real-time |
| **Xem lời giải** | Toggle tức thì, re-typeset MathJax sau khi mở |
| **Responsive** | Tương thích mobile, tablet, desktop |

---

## Thiết kế

Áp dụng phong cách **Minimalist Monochrome** — đen trắng tuyệt đối, serif editorial (Playfair Display + Source Serif 4 + JetBrains Mono), bo góc 0px, inversion hover tức thì, texture giấy hai lớp.
