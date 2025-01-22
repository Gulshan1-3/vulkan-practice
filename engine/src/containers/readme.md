Dynamic Array Implementation Guide
This guide explains the memory layout and pointer arithmetic used in our custom dynamic array implementation, which includes metadata headers for capacity, length, and element stride.
Memory Layout
The dynamic array consists of two main sections:

1.Header (Metadata)
2.Data Section


| Address    | Content          | Description                 

| 0x1000     | DARRAY_CAPACITY  | Metadata: Max elements (10) 
| 0x1008     | DARRAY_LENGTH    | Metadata: Current length (0)
| 0x1010     | DARRAY_STRIDE    | Metadata: Size per element 
| 0x1018     | Element 1        | Data section (start of array)
| 0x101C     | Element 2        | Next element in array       
| ...        | ...              | Remaining array elements    


Pointer Operations
Basic Pointer Representation

Array pointer (array) points to the start of the data section (0x1018)
Header pointer can be derived from the array pointer

Accessing the Header
To access the header from the array pointer:

header = (u64*)array - DARRAY_FIELD_LENGTH
// Example: (u64*)0x1018 - 3 = 0x1000 (points to start of header)


Header Field Access

header[0] = DARRAY_CAPACITY  // Access capacity at 0x1000
header[1] = DARRAY_LENGTH    // Access length at 0x1008
header[2] = DARRAY_STRIDE    // Access stride at 0x1010


Updating Header Fields
Example of updating the length field:

*darray_field_set(array, DARRAY_FIELD_LENGTH, 5);
// Updates value at 0x1008 to 5

Array Resizing
Before Resize


 Header (Metadata)                | Data Section        

 CAPACITY = 4                     | Element 1           
 LENGTH = 4                       | Element 2           
 STRIDE = 8                       | Element 3           
                                 | Element 4           



After Resize

 Header (Metadata)                | Data Section                

 CAPACITY = 8                     | Element 1, Element 2        
 LENGTH = 4                       | Element 3, Element 4        
 STRIDE = 8                       | Empty slots...             



Implementation Notes

The header and data sections are contiguous in memory
Pointer arithmetic is used to navigate between header and data sections
All header fields are 64-bit integers (u64)
The stride value determines the size of each element in bytes

Memory Safety Considerations

Always validate pointer arithmetic operations
Ensure proper alignment when accessing header fields
Check capacity before adding new elements
Maintain proper length tracking during operations

Common Operations

Initialization:

Allocate memory for header + initial capacity
Set initial header values
Return pointer to data section


Resizing:

Allocate new block with larger capacity
Copy header and existing elements
Update capacity in header
Free old memory block


Element Access:

Use stride value for proper element indexing
Validate index against current length
Apply pointer arithmetic based on stride



Remember to always maintain the relationship between the header and data sections when performing operations on the array.

