# AGENTS.md — Aturan Pemeliharaan Skills Library (`AG-Seeker`)

Repositori ini adalah pustaka pusat skill (*single source of truth*) dari hasil eksplorasi berbagai proyek.

## Aturan Struktur & Dokumentasi
1. **Flat 1 Level per Skill:** Setiap skill berada di `skills/<nama-skill>/SKILL.md` (standar lintas-agent: Antigravity, Claude Code, Codex, OpenCode). Jangan membuat folder bersarang `kategori/sub-skill/`.
2. **Progressive Disclosure:**
   - Frontmatter `name` & `description`: singkat, spesifik, menjelaskan *kapan* skill dipanggil.
   - `SKILL.md`: aturan wajib & langkah kerja inti (±50–120 baris).
   - `references/`: checklist panjang, contoh skrip, atau detail teknis tambahan.
3. **Update vs. Buat Baru:**
   - Jika menemukan bug atau pelajaran baru pada domain yang sudah ada (misalnya UX mobile Laravel atau kompresi WebP), **perbarui skill yang relevan**—jangan membuat skill baru untuk tiap 1 bug kecil.
   - Buat folder skill baru hanya untuk domain kerja yang berbeda (misalnya `instabox-vm`, `remotion-video`, `craft-anything-rebuild`).
4. **Sinkronisasi ke Antigravity:** Setelah mengubah atau menambah skill di `skills/<nama-skill>/`, perbarui juga di Skill Store Antigravity (`save_skill` / `update_skill`) agar aktif lintas percakapan.
