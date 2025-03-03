import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

public class VaultKafkaCredentialProviderTest {

    private static final String ENVIRONMENT_NAME = "DEV1"; // Example environment
    private static final String KAFKA_SERVICE_ID_CASH_LLE = "cash_lle";
    private static final String KAFKA_SERVICE_ID_EEH_LLE = "eeh_lle";
    private static final String ROLE_ID = "test-role-id";

    @BeforeEach
    public void setup() {
        // Mock the static methods
        try (MockedStatic<VaultSecretRoleIdProvider> mockedRoleIdProvider = Mockito.mockStatic(VaultSecretRoleIdProvider.class);
             MockedStatic<VaultFactory> mockedVaultFactory = Mockito.mockStatic(VaultFactory.class)) {

            // Mock roleId provider
            mockedRoleIdProvider.when(VaultSecretRoleIdProvider::getRoleId).thenReturn(ROLE_ID);

            // Mock VaultContext
            VaultContext mockVaultContext = mock(VaultContext.class);
            when(mockVaultContext.getSecret(anyString(), eq("corp"), eq(VaultEngine.LDAP)))
                    .thenReturn(new HashMap<>());

            // Mock VaultFactory
            mockedVaultFactory.when(() -> VaultFactory.getInstance(ROLE_ID)).thenReturn(mockVaultContext);
        }
    }

    @Test
    public void testGetKafkaCredentialCash() throws VaultConfigurationException, IOException {
        // Arrange
        Map<String, String> expectedCredentials = new HashMap<>();
        expectedCredentials.put("key", "value");

        try (MockedStatic<VaultKafkaCredentialProvider> mockedProvider = Mockito.mockStatic(VaultKafkaCredentialProvider.class)) {
            mockedProvider.when(() -> VaultKafkaCredentialProvider.getServiceAccountCash()).thenReturn(KAFKA_SERVICE_ID_CASH_LLE);

            // Act
            Map<String, String> credentials = VaultKafkaCredentialProvider.getKafkaCredentialCash();

            // Assert
            assertEquals(expectedCredentials, credentials);
        }
    }

    @Test
    public void testGetKafkaCredentialEeh() throws VaultConfigurationException, IOException {
        // Arrange
        Map<String, String> expectedCredentials = new HashMap<>();
        expectedCredentials.put("key", "value");

        try (MockedStatic<VaultKafkaCredentialProvider> mockedProvider = Mockito.mockStatic(VaultKafkaCredentialProvider.class)) {
            mockedProvider.when(() -> VaultKafkaCredentialProvider.getServiceAccountEeh()).thenReturn(KAFKA_SERVICE_ID_EEH_LLE);

            // Act
            Map<String, String> credentials = VaultKafkaCredentialProvider.getKafkaCredentialEeh();

            // Assert
            assertEquals(expectedCredentials, credentials);
        }
    }
}
