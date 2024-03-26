# MD-2 Diskette Format Notes

MD-2 diskettes are called "43FD" format, and the drive in the device is a type 43FD. This drive uses the same interface as 33FD (single sided) and 53FD (double sided) diskette drives and is (as far as we know) compatible with 53FD diskettes. The MD-2 can copy "53FD" diskettes in MFM mode, but for some reason the MD-2 software only supports copying 53FD diskettes in 256 byte sector format, and not the more common 1024 byte sector mode. Since the drive can do MFM, the sector size limitation may be in place because of the diskette controller hardware or RAM limitations in the MD-2.

MD Diskette images are 590,336 bytes in size.

The diskette uses a nonstandard filesystem with a simplified file layout and an extremely simple VTOC format.

Cylinder 0 is a standard IBM "Cylinder 0" VTOC but only shows the disk as having one file that allocates all data cylinders on the diskette. This prevents the disk from being overwritten if inserted into another machine type, as the system will see the disk as full and (probably, need to check) write protected so that it won't be overwritten.

All cylinders (other than 0) are 15 256byte sectors per side, and double sided. The MD-2 does not refer to data sectors by cylinder/head/sector but by a simplified "extent" address that is the cylinder number (1 byte hex from 01-4C) and the sector number from 01-1E, where sectors 01-0F are on head 0 and sectors 10-1E are on head 1.

The disk filesystem allocates data in multiples of 256 byte sectors.

The VTOC contains only 3 items per entry:
```
filename (8 chars EBCDIC, uppercase)
Starting extent (2 hex bytes from 0101 through 4C1E)
Ending extent (2 hex bytes from 0101 through 4C1E)
```

Ending extent is INCLUSIVE. File data includes the contents of that last sector. File that only occupy one sector have the same values for starting and ending extent. 

The disk VTOC is the last allocated file on the diskette but can occur anywhere on the diskette depending on the other contents of the diskette.

The MD-2 LISTVTOC utility lists the VTOC as a file on diskette with the name DIRECTORY. It will be the last file shown in the LISTVTOC output. A pointer to the VTOC has to be stored somewhere in the lower part of disk, presumably, so the MD knows where to find it.

The following is the specific diskette format, in Greaseweazle's "diskdefs" format.

### This is the 43FD format diskette used in the MD-2
```disk ibm.8d2.sd
    cyls = 77
    heads = 2
    tracks 0 ibm.fm
        secs = 26
        bps = 128
        rpm = 360
    end
    tracks * ibm.fm
        secs = 15
        bps = 256
        rpm = 360
    end
end
```
