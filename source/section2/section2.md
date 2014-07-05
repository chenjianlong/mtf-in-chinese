# 设计目标

This sections describes the design goals employed in the development of the Microsoft Tape Format.
• Fast retrieval of stored data.
• Low processing overhead to ensure optimum performance on low-end systems and devices. This is accomplished by
careful design of the control structures to reduce the amount of interpretation the application software needs to do.
• Allows applications to ignore information on the media that is not understood by the target operating system. This
feature makes it possible to restore data across platforms (e.g., data backed up on an Apple Macintosh system may be
        restored to a DOS system, ignoring the resource fork which DOS does not understand).
• The ability to extend the format for specialized processing by adding new DBLKs and data streams without rendering
the format unreadable by other applications. Applications which are not aware of the extensions can easily skip over
them, both increasing backward/forward compatibility, and allowing the restoration of data from media created by
another vendor's application.
• Data structures are arranged so that 32-bit values are aligned on 32-bit boundaries, and 16-bit values are aligned on 16-
bit boundaries. This is important because some processors require this alignment to run at maximum efficiency. By
making sure this alignment is followed it is easier for the implementor to map these structures directly onto data buffers.
• Reliable end of media handling.
• The ability to restore any remaining portion of a Data Set which spans multiple media (tapes or disks) in the event one
or more media is lost or damaged.
• Format support to deal with corrupt files encountered on the primary storage volume that is being written to removable
media.
• Support for unlimited directory path and file name lengths.
• 64-bit file data sizes.
• Allows the application to take full advantage of a drive's capabilities (e.g., Block Seek, Fast Seek to End of Data, etc.)
without hindering less capable drives.
