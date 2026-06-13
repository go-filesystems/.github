<p align="center"><img src="https://raw.githubusercontent.com/go-filesystems/brand/main/social/go-filesystems.png" alt="go-filesystems" width="640"></p>

<h1 align="center">go-filesystems</h1>
<p align="center">Pure-Go filesystem drivers — read & write disk images, no cgo, no root.</p>
<p align="center">[![docs](https://img.shields.io/badge/docs-mkdocs--material-0A6E96?style=flat-square&logo=materialformkdocs&logoColor=white)](https://go-filesystems.github.io/docs/) ![drivers](https://img.shields.io/badge/drivers-12-0079A8?style=flat-square) ![Go](https://img.shields.io/badge/Go-1.25-00ADD8?style=flat-square&logo=go&logoColor=white) ![license](https://img.shields.io/badge/license-MIT-0A6E96?style=flat-square)</p>

---

## What is this?

`go-filesystems` is a set of independent, dependency-free Go modules that read and
write on-disk filesystem images — one module per filesystem, all implementing
a small shared contract. No cgo, no root.

The contract lives in [`go-filesystems/interface`](https://github.com/go-filesystems/interface):

```go
// package filesystem
type Filesystem interface {
	Close() error
	ReadFile(path string) ([]byte, error)
	ListDir(path string) ([]DirEntry, error)
	Stat(path string) (Stat, error)
	WriteFile(path string, data []byte, perm os.FileMode) error
	ReadLink(path string) (string, error)
	MkDir(path string, perm os.FileMode) error
	DeleteFile(path string) error
	DeleteDir(path string) error
	Rename(oldPath, newPath string) error
}
```

## Filesystem drivers (12)

| | Module | Filesystem |
|---|---|---|
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-apfs.png" width="34"> | [`apfs`](https://github.com/go-filesystems/apfs) | Apple File System. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-btrfs.png" width="34"> | [`btrfs`](https://github.com/go-filesystems/btrfs) | Copy-on-write Linux fs with snapshots and subvolumes. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-exfat.png" width="34"> | [`exfat`](https://github.com/go-filesystems/exfat) | Extended FAT for large media. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ext4.png" width="34"> | [`ext4`](https://github.com/go-filesystems/ext4) | Linux ext4 — extents, 64-bit, journaling, metadata_csum. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-fat32.png" width="34"> | [`fat32`](https://github.com/go-filesystems/fat32) | FAT with 32-bit allocation. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-iso9660.png" width="34"> | [`iso9660`](https://github.com/go-filesystems/iso9660) | ISO 9660 / ECMA-119 optical-disc filesystem. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ntfs.png" width="34"> | [`ntfs`](https://github.com/go-filesystems/ntfs) | Windows NT filesystem. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-squashfs.png" width="34"> | [`squashfs`](https://github.com/go-filesystems/squashfs) | Compressed read-only archive filesystem. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-uefi.png" width="34"> | [`uefi`](https://github.com/go-filesystems/uefi) | EFI System Partition (FAT-based). |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ufs.png" width="34"> | [`ufs`](https://github.com/go-filesystems/ufs) | Unix File System (BSD). |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-xfs.png" width="34"> | [`xfs`](https://github.com/go-filesystems/xfs) | High-performance journaling filesystem. |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-zfs.png" width="34"> | [`zfs`](https://github.com/go-filesystems/zfs) | Copy-on-write pooled storage filesystem. |

> More drivers are added over time. This list reflects the repos that
> actually exist in the org.

## Support matrix

Read = open & inspect images · Write = mutate in place · Format = create a fresh image.
Per-driver details on the [docs site](https://go-filesystems.github.io/docs/drivers/).

| Module | Read | Write | Format | Label | Symlinks | On-disk format |
|---|:--:|:--:|:--:|:--:|:--:|---|
| [`apfs`](https://github.com/go-filesystems/apfs) | ✅ | ✅ | ✅ | — | ✅ | Real APFS on-disk (kext-mountable), GPT-aware |
| [`btrfs`](https://github.com/go-filesystems/btrfs) | ✅ | ✅ | ✅ | ✅ | ✅ | Single-device, CRC32c (btrfs-progs ≥ 5.x) |
| [`exfat`](https://github.com/go-filesystems/exfat) | ✅ | ✅ | ✅ | ✅ | ✕ | exFAT |
| [`ext4`](https://github.com/go-filesystems/ext4) | ✅ | ✅ | ✅ | ✅ | ✅ | ext4 — extents, 64-bit, flex_bg, dir htree, metadata_csum (CRC32c) |
| [`fat32`](https://github.com/go-filesystems/fat32) | ✅ | ✅ | ✅ | ✅ | ✕ | FAT32 |
| [`iso9660`](https://github.com/go-filesystems/iso9660) | ✅ | ✕ | ✕ | — | ✅ | ISO 9660 / ECMA-119 + Rock Ridge (names/perms/symlinks) + Joliet (UCS-2 names) |
| [`ntfs`](https://github.com/go-filesystems/ntfs) | ✅ | ✅ | ✅ | ✅ | ✕ | Minimal in-image blob model — NOT the real NTFS on-disk format |
| [`squashfs`](https://github.com/go-filesystems/squashfs) | ✅ | ✕ | ✕ | — | ✅ | SquashFS 4.0 read-only archive; gzip/xz/zstd/lzo/lz4 blocks + fragments |
| [`uefi`](https://github.com/go-filesystems/uefi) | ✅ | ✅ | ✅ | — | — | OVMF/EDK2 NvVar variable store; time-based authenticated writes |
| [`ufs`](https://github.com/go-filesystems/ufs) | ✅ | ✅ | ✅ | — | ✅ | UFS2 (FreeBSD 14.x) read+write; UFS1 read |
| [`xfs`](https://github.com/go-filesystems/xfs) | ✅ | ✅ | ✅ | ✅ | ✅ | XFS v5 (CRC32c, ftype) |
| [`zfs`](https://github.com/go-filesystems/zfs) | ✅ | ✅ | ✅ | — | — | Single pool / single vdev (test-oriented subset) |

Read = open & inspect · Write = mutate in place · Format = create a fresh image · Label = volume label (`Labeller`).

## Links

- 📖 Docs — <https://go-filesystems.github.io/docs/>
- 🌐 Site — <https://go-filesystems.github.io/>
- 🧩 Interface — <https://github.com/go-filesystems/interface>
- 🎨 Brand assets — <https://github.com/go-filesystems/brand>

---
<p align="center"><sub>Branding in <a href="https://github.com/go-filesystems/brand">go-filesystems/brand</a>.</sub></p>
