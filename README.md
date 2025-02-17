import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class DecryptIdPasswordFileTest {

    private DecryptIdPasswordFile decryptor;
    private EncryptIdPasswordFile mockEncryptor;
    private Decrypt mockDecrypt;
    private Data mockData;

    @BeforeEach
    void setUp() {
        mockEncryptor = mock(EncryptIdPasswordFile.class);
        mockDecrypt = mock(Decrypt.class);
        mockData = mock(Data.class);
        decryptor = new DecryptIdPasswordFile("testEnv", "testFile");
    }

    @Test
    void testFileNotFoundThrowsException() {
        when(mockEncryptor.fileExists(any())).thenReturn(false);
        Exception exception = assertThrows(Exception.class, () -> {
            new DecryptIdPasswordFile("testEnv", "nonexistentFile");
        });
        assertTrue(exception.getMessage().contains("Unable to find"));
    }

    @Test
    void testGetUserName() throws Exception {
        byte[] fakeUserData = "user123".getBytes();
        when(mockData.geta1z64r()).thenReturn(fakeUserData);
        when(mockData.geta9f5e4()).thenReturn("key1".getBytes());
        when(mockData.getAdf3f4asdf1()).thenReturn("key2".getBytes());
        when(mockDecrypt.getDecryptedString(fakeUserData, "key1".getBytes(), "key2".getBytes())).thenReturn("decryptedUser");
        
        assertEquals("decryptedUser", decryptor.getUserName());
    }

    @Test
    void testGetPassword() throws Exception {
        byte[] fakePasswordData = "pass123".getBytes();
        when(mockData.geta3n5m2pq10()).thenReturn(fakePasswordData);
        when(mockData.geta9f5e4()).thenReturn("key1".getBytes());
        when(mockData.getAdf3f4asdf1()).thenReturn("key2".getBytes());
        when(mockDecrypt.getDecryptedString(fakePasswordData, "key1".getBytes(), "key2".getBytes())).thenReturn("decryptedPassword");
        
        assertEquals("decryptedPassword", decryptor.getPassword());
    }
}
