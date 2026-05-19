# Test Profile Feature

## Changes Summary

Code đã được sửa để lưu cookies/session giữa các lần chạy:

### 1. Browser Profile Storage
- **Vị trí lưu**: `~/.reverse-api/profiles/<profile_name>/`
- **Mặc định**: profile name là `"default"`
- **Không xóa**: Profile được giữ lại sau khi đóng browser

### 2. Cách dùng

#### CLI Mode
```bash
# Dùng default profile
reverse-api-engineer manual --prompt "test shopee" --url "https://shopee.vn"

# Dùng profile khác (ví dụ: shopee)
reverse-api-engineer manual --prompt "test shopee" --url "https://shopee.vn" --profile shopee

# Profile khác cho từng site
reverse-api-engineer manual --prompt "test youtube" --url "https://youtube.com" --profile youtube
```

#### Interactive Mode
```bash
reverse-api-engineer
# Nhập prompt và url như bình thường
# Sẽ dùng profile "default" tự động
```

### 3. Kiểm tra profile đã lưu
```bash
ls -la ~/.reverse-api/profiles/
```

### 4. Xóa profile (nếu cần reset cookies)
```bash
rm -rf ~/.reverse-api/profiles/default
# hoặc
rm -rf ~/.reverse-api/profiles/shopee
```

## Files Changed
1. `/src/reverse_api/browser.py`
   - Thêm `PERSISTENT_PROFILE_DIR` constant
   - Thêm `get_persistent_profile_dir()` function
   - Sửa `ManualBrowser.__init__()` để nhận `profile_name` param
   - Sửa `_start_with_real_chrome()` để dùng persistent dir thay vì temp

2. `/src/reverse_api/cli.py`
   - Thêm `--profile` option cho `manual` command
   - Sửa `run_manual_capture()` để nhận `profile_name` param

## Workflow
1. Lần đầu chạy: Login như bình thường
2. Cookies/session lưu vào `~/.reverse-api/profiles/<profile_name>/`
3. Lần sau chạy với cùng profile: Tự động load cookies, không cần login lại
