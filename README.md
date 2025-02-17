import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.io.IOException;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

class CashCoreUtilTest {

    @Mock
    private Transaction mockTransaction;

    @Mock
    private CutEncryptionHelper mockEncryptionHelper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        when(mockEncryptionHelper.getInstance()).thenReturn(mockEncryptionHelper);
        when(mockEncryptionHelper.encrypt(anyString())).thenReturn("encryptedValue");
    }

    @Test
    void testEncryptTransaction_Success() throws IOException {
        // Mock transaction behavior
        when(mockTransaction.getDbtrFIAccountNumber()).thenReturn("123456789");
        when(mockTransaction.getCdrtFIAccountNumber()).thenReturn("987654321");
        when(mockTransaction.getCashCtrlStVal("Pan")).thenReturn("testPan");
        when(mockTransaction.getCrdrtrCtrlStVal("Pan")).thenReturn("testPan");
        when(mockTransaction.getDbtrCtrlStVal("Pan")).thenReturn("testPan");

        // Call the method under test
        assertDoesNotThrow(() -> CashCoreUtil.encryptTransaction(mockTransaction));

        // Verify encryption and updates
        verify(mockTransaction).setDbtrFIAccountNumber("encryptedValue");
        verify(mockTransaction).setCdrtFIAccountNumber("encryptedValue");
        verify(mockTransaction).upsertExtndInfoCashCtrlSt("Pan", "encryptedValue");
        verify(mockTransaction).upsertExtndInfoCrdrtrPmtAdvPstCtrlSt("Pan", "encryptedValue");
        verify(mockTransaction).upsertExtndInfoDbtrPmtAdvPstCtrlSt("Pan", "encryptedValue");
    }

    @Test
    void testEncryptTransaction_HandlesNullValues() throws IOException {
        // Mock transaction behavior with nulls
        when(mockTransaction.getDbtrFIAccountNumber()).thenReturn(null);
        when(mockTransaction.getCdrtFIAccountNumber()).thenReturn(null);
        when(mockTransaction.getCashCtrlStVal("Pan")).thenReturn(null);

        // Call the method under test
        assertDoesNotThrow(() -> CashCoreUtil.encryptTransaction(mockTransaction));

        // Ensure encryption wasn't called for null values
        verify(mockTransaction, never()).setDbtrFIAccountNumber(anyString());
        verify(mockTransaction, never()).setCdrtFIAccountNumber(anyString());
        verify(mockTransaction, never()).upsertExtndInfoCashCtrlSt(anyString(), anyString());
    }

    @Test
    void testEncryptTransaction_ThrowsIOExceptionOnEncryptionFailure() {
        // Mock exception during encryption
        when(mockTransaction.getDbtrFIAccountNumber()).thenReturn("123456789");
        when(mockEncryptionHelper.encrypt(anyString())).thenThrow(new RuntimeException("Encryption failed"));

        // Verify IOException is thrown
        IOException exception = assertThrows(IOException.class, () -> {
            CashCoreUtil.encryptTransaction(mockTransaction);
        });

        assertTrue(exception.getMessage().contains("Failed to encrypt account numbers"));
    }
}
