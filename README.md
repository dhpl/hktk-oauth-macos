# HKTK OAuth SDK for macOS

SDK OAuth native cho game macOS và Cocos, phát hành dưới dạng C/C++ universal library. Phiên bản hiện tại là `1.3.12`.

## Yêu cầu

- macOS 10.15 trở lên trên Intel
- macOS 11 trở lên trên Apple Silicon
- Kiến trúc `x86_64` và `arm64`

## Cài đặt bằng CMake

Thêm repo vào project, ví dụ bằng Git submodule:

```bash
git submodule add https://github.com/dhpl/hktk-oauth-macos.git third_party/hktk-oauth-macos
```

Link static library:

```cmake
add_subdirectory(third_party/hktk-oauth-macos)
target_link_libraries(your_game PRIVATE HKTKSDK::Static)
```

Có thể dùng `HKTKSDK::Shared` nếu muốn link dynamic library. Khi đó cần embed và sign `libHKTKSDK.dylib` trong app bundle.

## C++/Cocos

```cpp
#include <HKTKSDK.hpp>

hktk::Config config;
config.environment = hktk::Environment::Production;
config.clientId = "YOUR_CLIENT_ID";

hktk::Client client(config);
auto login = client.loginAsync();

// Chờ kết quả trên worker thread, sau đó dispatch về Cocos thread.
auto result = login.get();
```

SDK mở trình duyệt mặc định, nhận OAuth callback qua loopback và trả `code`, `state`, `redirectUri`. Backend game dùng nguyên văn `redirectUri` để đổi code lấy token. Không đặt `clientSecret` trong game.

Đăng ký redirect URI sau trong Partner App:

```text
http://127.0.0.1/hktk-callback
```

Nếu app bật App Sandbox, thêm entitlement `com.apple.security.network.server` để nhận callback loopback.

Tài liệu API: [docs.hktk.vn/oauth/hktk-auth-api.html](https://docs.hktk.vn/oauth/hktk-auth-api.html)

## C API

Header `hktk_sdk.h` cung cấp các hàm `hktk_client_create`, `hktk_client_login`, `hktk_client_cancel` và `hktk_client_destroy`.
