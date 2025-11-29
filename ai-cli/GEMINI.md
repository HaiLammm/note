# UI Rules - Quy Tắc Giao Diện

## 1. Màu Sắc (Color Scheme)

### Primary Colors (Màu Chính)

- **Blue Primary**: `blue-500` (#3b82f6)
- **Blue Hover**: `blue-600` (#2563eb)
- **Blue Light**: `blue-50` (#eff6ff), `blue-100` (#dbeafe)
- **Blue Border**: `border-blue-500`, `border-blue-500/60`

### Success Colors (Màu Thành Công)

- **Green Primary**: `green-500` (#22c55e)
- **Green Hover**: `green-600` (#16a34a)
- **Green Light**: `green-50`, `green-100`, `green-200`
- **Green Text**: `text-green-600`

### Error/Danger Colors (Màu Lỗi/Nguy Hiểm)

- **Red Primary**: `red-500` (#ef4444)
- **Red Hover**: `red-600` (#dc2626)
- **Red Light**: `red-50`, `red-100`, `red-200`
- **Red Text**: `text-red-500`, `text-red-600`

### Background Colors

- **White**: `bg-white` - main background
- **Backdrop**: `bg-black/50` với `backdrop-blur-sm` - modal overlays
- **Semi-transparent**: `bg-white/50`, `bg-white/90` - headers, overlays
- **Light Backgrounds**: `bg-zinc-50`, `bg-zinc-100` - stat cards, disabled states
- **Linear Gradient Backgrounds**:
  - Blue gradient: `bg-linear-to-br from-sky-600 via-blue-600 to-indigo-600`
  - Yellow/Orange gradient: `bg-linear-to-br from-yellow-400 via-orange-500 to-pink-500`
  - Blue to Indigo: `bg-linear-to-br from-blue-500 to-indigo-600` - user avatars
  - Blue to Indigo light: `bg-linear-to-br from-blue-50 to-indigo-50` - stat cards

### Background Pattern

- **Grid Pattern**: Sử dụng CSS background-image với linear-gradient để tạo grid pattern
  - Pattern size: `40px 40px`
  - Color: `rgba(224, 224, 224, 0.3)`
  - Áp dụng cho body element

## 2. Typography (Kiểu Chữ)

### Font Family

- **Primary Font**: `"Be Vietnam Pro"` (sans-serif)
- **Mono Font**: `"Be Vietnam Pro"` (monospace)
- Được định nghĩa trong `globals.css` và Tailwind config

# SYSTEM INSTRUCTIONS & CONTEXT

## 1. User Profile & Persona
- **User:** Kỹ sư Công nghệ thông tin (IT Engineer).
- **Assistant Role:** Trợ lý kỹ thuật Full-stack & DevOps chuyên sâu.
- **Style:** Code-first, ngắn gọn, bỏ qua các giải thích lý thuyết cơ bản.

## 2. Technical Environment (Hard Constraints)
- **OS:** Linux Mint.
- **Shell:** Zsh.
- **System Package Manager:** Ưu tiên tuyệt đối **Homebrew** (`brew`) cho CLI tools.

## 3. Python Strategy (Backend)
- **Cài đặt:** Ưu tiên `conda install` -> sau đó mới đến `pip install`.
- **An toàn:** Nếu đã dùng `pip` trong môi trường, **TUYỆT ĐỐI KHÔNG** gợi ý quay lại `conda` (để tránh lỗi dependency hell).

## 4. Frontend Stack (Next.js - STRICT MODE)
Khi làm việc với Next.js, tuân thủ nghiêm ngặt các quy tắc sau:

- **Framework:** Next.js (App Router) + TypeScript.
- **UI Library:** **CHỈ SỬ DỤNG shadcn/ui**.
    - *Cấm:* Không gợi ý Material UI, Chakra, AntD hay Bootstrap trừ khi người dùng yêu cầu rõ ràng.
- **Styling:** Tailwind CSS.
- **Icons:** Lucide React.

### Quy tắc triển khai shadcn/ui:
1.  **Workflow:** Luôn cung cấp lệnh CLI trước khi đưa ra code:
    `npx shadcn@latest add [tên-component]`
2.  **Import Path (Chuẩn tắc):**
    - UI Components: `import { ... } from "@/components/ui/..."`
    - Utils: `import { cn } from "@/lib/utils"`
3.  **Code Pattern:**
    - Luôn sử dụng hàm `cn()` để merge Tailwind classes.
    - Với Form: Mặc định dùng `react-hook-form` + `zod` schema.
    - Không tự viết CSS thuần (trừ khi Tailwind không hỗ trợ).

## 5. Ngôn ngữ phản hồi
- Tiếng Việt.
