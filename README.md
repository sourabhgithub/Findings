import org.apache.http.impl.client.CloseableHttpClient;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockedStatic;
import org.mockito.Mockito;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpHeaders;
import org.springframework.util.CollectionUtils;
import org.springframework.util.ResourceUtils;

import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.cert.CertificateException;
import java.util.Collections;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.*;

class RestClientConfigTest {

    @InjectMocks
    private RestClientConfig restClientConfig;

    @Mock
    private HttpDynamicConfigPropertyRepository httpDynamicConfigPropertyRepository;

    @Mock
    private OltpDynamicConfigPropertyRepository oltpDynamicConfigPropertyRepository;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testCreateHttpClient() throws Exception {
        // Mock system properties
        System.setProperty("KEYSTORE_PATH", "dummyKeyStorePath");
        System.setProperty("javax.net.ssl.trustStore", "dummyTrustStorePath");
        System.setProperty("javax.net.ssl.trustStorePassword", "dummyTrustStorePassword");

        // Mock static methods
        try (MockedStatic<CashCoreUtil> mockedCashCoreUtil = Mockito.mockStatic(CashCoreUtil.class);
             MockedStatic<ResourceUtils> mockedResourceUtils = Mockito.mockStatic(ResourceUtils.class)) {

            // Mock CashCoreUtil.retrieveKeystorePassword
            mockedCashCoreUtil.when(() -> CashCoreUtil.retrieveKeystorePassword(anyString(), anyString()))
                    .thenReturn("dummyPassword".toCharArray());

            // Mock ResourceUtils.getURL
            mockedResourceUtils.when(() -> ResourceUtils.getURL(anyString()))
                    .thenReturn(new URL("file://dummyPath"));

            // Call the method under test
            CloseableHttpClient httpClient = restClientConfig.httpClient();

            // Assertions
            assertNotNull(httpClient);
        }
    }

    @Test
    void testCreateKeyStore() throws Exception {
        URL dummyUrl = new URL("file://dummyPath");
        char[] dummyPassword = "dummyPassword".toCharArray();

        try (MockedStatic<ResourceUtils> mockedResourceUtils = Mockito.mockStatic(ResourceUtils.class)) {
            mockedResourceUtils.when(() -> ResourceUtils.getURL(anyString())).thenReturn(dummyUrl);

            KeyStore keyStore = restClientConfig.createKeyStore(dummyUrl, dummyPassword);
            assertNotNull(keyStore);
        }
    }

    @Test
    void testRestUrl() {
        OltpDynamicConfigProperty property = new OltpDynamicConfigProperty();
        property.setPropValue("http://dummyUrl");
        when(oltpDynamicConfigPropertyRepository.findByPropNameIgnoreCase(anyString()))
                .thenReturn(Collections.singletonList(property));

        String url = restClientConfig.restUrl();
        assertEquals("http://dummyUrl/cash_business_svc/cash/settle/transaction", url);
    }

    @Test
    void testIsKafkaOn() {
        OltpDynamicConfigProperty property = new OltpDynamicConfigProperty();
        property.setPropValue("true");
        when(oltpDynamicConfigPropertyRepository.findByPropNameIgnoreCase(anyString()))
                .thenReturn(Collections.singletonList(property));

        boolean isKafkaOn = restClientConfig.isKafkaOn();
        assertTrue(isKafkaOn);
    }

    @Test
    void testIsKafkaSchemaOn() {
        OltpDynamicConfigProperty property = new OltpDynamicConfigProperty();
        property.setPropValue("true");
        when(oltpDynamicConfigPropertyRepository.findByPropNameIgnoreCase(anyString()))
                .thenReturn(Collections.singletonList(property));

        boolean isKafkaSchemaOn = restClientConfig.isKafkaSchemaOn();
        assertTrue(isKafkaSchemaOn);
    }

    @Test
    void testGetHeaders() {
        OltpDynamicConfigProperty userNameProperty = new OltpDynamicConfigProperty();
        userNameProperty.setPropValue("encryptedUser");
        OltpDynamicConfigProperty passwordProperty = new OltpDynamicConfigProperty();
        passwordProperty.setPropValue("encryptedPassword");

        when(oltpDynamicConfigPropertyRepository.findByPropNameIgnoreCase(anyString()))
                .thenReturn(Collections.singletonList(userNameProperty))
                .thenReturn(Collections.singletonList(passwordProperty));

        HttpHeaders headers = restClientConfig.getHeaders();
        assertNotNull(headers);
        assertTrue(headers.containsKey("Authorization"));
    }
}
