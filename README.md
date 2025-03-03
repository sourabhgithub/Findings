import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.io.IOException;
import java.util.Map;

public class VaultRefkaCredentialProviderTest {

    @Mock
    private VaultSecretRoleIdProvider vaultSecretRoleIdProvider;

    @Mock
    private VaultFactory vaultFactory;

    @Mock
    private VaultContext vaultContext;

    @InjectMocks
    private VaultRefkaCredentialProvider vaultRefkaCredentialProvider;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGetRefkaCredentialCash() throws Exception {
        String roleId = "testRoleId";
        String serviceAccount = "testServiceAccount";
        Map<String, String> expectedSecrets = Map.of("key", "value");

        when(vaultSecretRoleIdProvider.getRoleId()).thenReturn(roleId);
        when(vaultFactory.getInstance(roleId)).thenReturn(vaultContext);
        when(vaultContext.getSecret(serviceAccount, "corp", VaultEngine.LDAP)).thenReturn(expectedSecrets);

        Map<String, String> result = vaultRefkaCredentialProvider.getRefkaCredentialCash();

        assertEquals(expectedSecrets, result);
        verify(vaultSecretRoleIdProvider).getRoleId();
        verify(vaultFactory).getInstance(roleId);
        verify(vaultContext).getSecret(serviceAccount, "corp", VaultEngine.LDAP);
    }

    @Test
    public void testGetRefkaCredentialEsh() throws Exception {
        String roleId = "testRoleId";
        String serviceAccount = "testServiceAccount";
        Map<String, String> expectedSecrets = Map.of("key", "value");

        when(vaultSecretRoleIdProvider.getRoleId()).thenReturn(roleId);
        when(vaultFactory.getInstance(roleId)).thenReturn(vaultContext);
        when(vaultContext.getSecret(serviceAccount, "corp", VaultEngine.LDAP)).thenReturn(expectedSecrets);

        Map<String, String> result = vaultRefkaCredentialProvider.getRefkaCredentialEsh();

        assertEquals(expectedSecrets, result);
        verify(vaultSecretRoleIdProvider).getRoleId();
        verify(vaultFactory).getInstance(roleId);
        verify(vaultContext).getSecret(serviceAccount, "corp", VaultEngine.LDAP);
    }
}
