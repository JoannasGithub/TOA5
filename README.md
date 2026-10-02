# TOA5
void decode_buffer(void *databuf, char *data) {
    void *ptr = databuf;
    int data_index = 0;

    while (1) {
        // First bytes of each chunk contain a size_t
        size_t size = *(size_t *)ptr;

        // A size of 0 means there are no more chunks
        if (size == 0) {
            break;
        }

        // Move past the size_t to reach the characters
        ptr += sizeof(size_t);

        // Copy this chunk's characters into data
        for (size_t i = 0; i < size; i++) {
            data[data_index] = *(char *)ptr;
            data_index++;
            ptr++;
        }

        // Skip padding so we reach the next 8-byte boundary
        size_t padding = (8 - (size % 8)) % 8;
        ptr += padding;
    }

    // C strings must end in '\0'
    data[data_index] = '\0';
}



PA
Pointer Math

In this recitation, you will implement a decoding function that uses pointer math to interpret bytes stored in memory in an a regular but unpredictable structure. You will learn about pointer math, raw memory access, and data representations.

Requirements

You must implement the decode_buffer() function in ptrmath.c. This function will be passed a buffer of type void * containing input data and a character array that you will fill using the input data.

The input data stores a sequence of characters that are split into several chunks. Each chunk consists of an unsigned integer of type size_t which stores the number of characters in the chunk followed immediately by character data consisting of the number of characters stored in the size_t, followed immediately by enough padding bytes to round the chunk out to a multiple of 8 bytes. An example chunk might look like this:

| (size_t)5 | 'H' | 'e' | 'l' | 'l' | 'o' | pad | pad | pad |

The final chunk will consist of a size_t containing the value 0 and no character data or padding.

To decode this information, you should copy the character data from each chunk into the given character array, followed by an ASCII NUL byte to produce a properly terminated C string. Note that the termination byte does not appear in the input chunks!

Building and Submission

The command make will build a binary called ptrmath that you can run. You should see a meaningful message if your decoder is working correctly on the provided input. If it is not, you will see junk, whitespace, your program will crash, etc.

You should submit only the file ptrmath.c to Auto grader.

Testing

You can test your program against the provided data.bin file by running ./ptrmath, as described above. You can also create messages of your own choosing by running ./encoder filename "message text", which will write the given message text into the named file according to the format specified in Requirements, and then test decoding of the message with ./ptrmath filename, where filename is the same file given to encoder.

Hints

Remember that pointer math on a void * pointer is in terms of bytes, while many data types (such as size_t!) are larger than one byte. You can add increments of sizeof(type) to adjust a void pointer by the size of another type.

Pointer casting works like casting of any other type. For example:

int readptr(void *ptr) {
    return *(int *)ptr;
}
This function accepts a void pointer argument, but interprets the data stored at the pointer as an int pointer. A void pointer cannot be directly dereferenced, you will have to cast it in order to read the data to which it points.

When calculating padding, consider the modulus operator (%).

Grading

Points will be awarded as follows:

1 pt: A single chunk with no padding is correctly decoded
2 pts: Multiple chunks with no padding are correctly decoded
2 pts: Arbitrary chunks are correctly decoded
