<p align="center"><img src="https://raw.githubusercontent.com/go-filesystems/brand/main/social/go-filesystems.png" alt="go-filesystems" width="640"></p>

<h1 align="center">go-filesystems</h1>
<p align="center">Pure-Go filesystem drivers — read & write disk images, no cgo, no root.</p>
<p align="center"><a href="https://go-filesystems.github.io/docs/"><img src="https://img.shields.io/badge/docs-mkdocs--material-0A6E96?style=flat-square&logo=materialformkdocs&logoColor=white" alt="docs"></a> <img src="https://img.shields.io/badge/drivers-12-0079A8?style=flat-square" alt="drivers"> <img src="https://img.shields.io/badge/Go-1.26-00ADD8?style=flat-square&logo=go&logoColor=white" alt="Go"> <img src="https://img.shields.io/badge/license-BSD--3--Clause-0A6E96?style=flat-square" alt="license"></p>

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

| | Module | Filesystem | On-disk format |
|---|---|---|---|
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-apfs.png" width="34"> | [`apfs`](https://github.com/go-filesystems/apfs) | Apple File System. | Real APFS on-disk (kext-mountable), GPT-aware |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-btrfs.png" width="34"> | [`btrfs`](https://github.com/go-filesystems/btrfs) | Copy-on-write Linux fs with snapshots and subvolumes. | Single-device, CRC32c (btrfs-progs ≥ 5.x) |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-exfat.png" width="34"> | [`exfat`](https://github.com/go-filesystems/exfat) | Extended FAT for large media. | exFAT |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ext4.png" width="34"> | [`ext4`](https://github.com/go-filesystems/ext4) | Linux ext4 — extents, 64-bit, journaling, metadata_csum. | ext4 — extents, 64-bit, flex_bg, dir htree, metadata_csum (CRC32c) |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-fat32.png" width="34"> | [`fat32`](https://github.com/go-filesystems/fat32) | FAT with 32-bit allocation. | FAT32 |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-iso9660.png" width="34"> | [`iso9660`](https://github.com/go-filesystems/iso9660) | ISO 9660 / ECMA-119 optical-disc filesystem. | ISO 9660 / ECMA-119 + Rock Ridge (names/perms/symlinks) + Joliet (UCS-2 names) |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ntfs.png" width="34"> | [`ntfs`](https://github.com/go-filesystems/ntfs) | Windows NT filesystem. | Minimal in-image blob model — NOT the real NTFS on-disk format |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-squashfs.png" width="34"> | [`squashfs`](https://github.com/go-filesystems/squashfs) | Compressed read-only archive filesystem. | SquashFS 4.0 read-only archive; gzip/xz/zstd/lzo/lz4 blocks + fragments |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-uefi.png" width="34"> | [`uefi`](https://github.com/go-filesystems/uefi) | EFI System Partition (FAT-based). | OVMF/EDK2 NvVar variable store; time-based authenticated writes |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-ufs.png" width="34"> | [`ufs`](https://github.com/go-filesystems/ufs) | Unix File System (BSD). | UFS2 (FreeBSD 14.x) read+write; UFS1 read; NetBSD/OpenBSD FFSv1/FFSv2 (via `ffs` alias module) |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-xfs.png" width="34"> | [`xfs`](https://github.com/go-filesystems/xfs) | High-performance journaling filesystem. | XFS v5 (CRC32c, ftype) |
| <img src="https://raw.githubusercontent.com/go-filesystems/brand/main/avatar/go-filesystems-zfs.png" width="34"> | [`zfs`](https://github.com/go-filesystems/zfs) | Copy-on-write pooled storage filesystem. | Single pool / single vdev (test-oriented subset) |

> More drivers are added over time. This list reflects the repos that
> actually exist in the org.

## Support matrix

Read = open & inspect images · Write = mutate in place · Format = create a fresh image.
Per-driver details on the [docs site](https://go-filesystems.github.io/docs/drivers/).

| Module | Read | Write | Format | Label | Symlinks | Quota | Snapshots | Encryption | Platform |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|---|
| [`apfs`](https://github.com/go-filesystems/apfs) | ✅ | ✅ | ✅ | — | ✅ | — | ✅ | ✕ | macOS / iOS |
| [`btrfs`](https://github.com/go-filesystems/btrfs) | ✅ | ✅ | ✅ | ✅ | ✅ | ✕ | ✕ | — | Linux |
| [`exfat`](https://github.com/go-filesystems/exfat) | ✅ | ✅ | ✅ | ✅ | ✕ | — | — | — | Windows / removable |
| [`ext4`](https://github.com/go-filesystems/ext4) | ✅ | ✅ | ✅ | ✅ | ✅ | ✕ | — | ✕ | Linux |
| [`fat32`](https://github.com/go-filesystems/fat32) | ✅ | ✅ | ✅ | ✅ | ✕ | — | — | — | cross-platform |
| [`iso9660`](https://github.com/go-filesystems/iso9660) | ✅ | ✕ | ✕ | — | ✅ | — | — | — | cross-platform (optical) |
| [`ntfs`](https://github.com/go-filesystems/ntfs) | ✅ | ✅ | ✅ | ✅ | ✕ | — | — | — | Windows |
| [`squashfs`](https://github.com/go-filesystems/squashfs) | ✅ | ✕ | ✅ | — | ✅ | — | — | — | Linux |
| [`uefi`](https://github.com/go-filesystems/uefi) | ✅ | ✅ | ✅ | — | — | — | — | — | firmware (UEFI) |
| [`ufs`](https://github.com/go-filesystems/ufs) | ✅ | ✅ | ✅ | — | ✅ | ✕ | ✕ | — | BSD |
| [`xfs`](https://github.com/go-filesystems/xfs) | ✅ | ✅ | ✅ | ✅ | ✅ | ✕ | — | ✕ | Linux |
| [`zfs`](https://github.com/go-filesystems/zfs) | ✅ | ✅ | ✅ | — | — | ✕ | ✕ | ✕ | illumos / BSD / Linux |

**Legend:** ✅ supported by the driver · ✕ not yet implemented (the on-disk format has the feature) · — not applicable (the format has no such feature).
Read = open & inspect · Write = mutate in place · Format = create a fresh image · Label = volume label (`Labeller`). Quota / Snapshots / Encryption reflect what the **driver** implements today.

## Links

- 📖 Docs — <https://go-filesystems.github.io/docs/>
- 🌐 Site — <https://go-filesystems.github.io/>
- 🧩 Interface — <https://github.com/go-filesystems/interface>
- 🎨 Brand assets — <https://github.com/go-filesystems/brand>

---
<p align="center"><sub>Branding in <a href="https://github.com/go-filesystems/brand">go-filesystems/brand</a>.</sub></p>

