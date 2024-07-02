# Initial Registration
Processo para registro de um UE em uma rede antes de usar os serviços de rede.

## 1. Registration Request (UE -> (R)AN)
O UE envia uma mensagem AN para o (R)AN contendo parametros como 5G-S-TMSI/GUAMI, NSSAI solicitado, PLMN.

## 2. AMF Selection
O (R)AN manda a mensagem de Registration Request enviada de UE para (R)AN usando a conexão existente. Se a mensagem não ter os parametros de 5G-S-TMSI ou GUAMI o (R)AN usa o RAT e NSSAI para encaminhar para AMF apropriado e caso o não conseguir selecionar esse AMF, ele envia para uma AMF padrão configurando no (R)AN.

## 3. Registration Request ((R)AN -> New AMF)
O (R)AN encaminha a mensagem N2 e seus parametros e a mensagem de Registration Request para o Novo AMF. A mensagem de Registration Request contem dados de acesso do UE, informação de de seleção de sessão PDU e solicitação de contexto UE. 

Se for usado o NG-(R)AN, ele ira encaminhar para o Novo AMF os parametros como ID PLMN e informação sobre localização.

## 4. (Opcional) Namf_Communication_UEContextTransfer (New AMF -> Old AMF)
**Se o AMF é relocado**, o novo AMF obtem o contexto UE, do AMF velho a partir da mensagem enviando uma mensagem de Namf_Communication_UEContextTransfer 

## 5. (Opcional) Namf_Communication_UEContextTransfer response (Old AMF -> New Amf)
O AMF antigo retorna a informação de contexto UE para o novo AMF por uma mensagem de Namf_Communication_UEContextTransfer

## 6. Identity Request (New AMF -> UE)
O novo AMF envia uma mensagem de Identity Request para o UE para obter o SUCI (Subscription Concealed Identifier)

## 7. Identity Response (UE -> New AMF)
O UE retorna o *Identity Response* contendo o SUCI para o novo AMF.

## 8. AUSF selection (new AMF -> AUSF)
O novo AMF seleciona um AUSF para autenticar o UE de acordo com o SUPI/SUCI

## 9. Authentication/Security
A autenticação é realizada nessa etapa

## 10. Namf_Communication_RegistrationCompleteNotify (New AMF -> Old AMF)
O novo AMF avisa ao AMF velho que o registro do UE foi feito no novo AMF.

## 11. Identity Request/Response (New AMF -> UE / UE -> New AMF)
O PEI (Permanent Equipment Identifier) precisa ser autenticado pelas politicas locais do AMF, e o novo AMF envia uma mensagem de *Identity Request* ao UE para obter esse PEI.

O UE envia de retorno uma mensagem de *Identity Response* o novo AMF

## 12. N5g-eir_EquipamentIdentityCheck_Get (New AMF -> EIR)
O novo AMF inicia uma checagem de identidade ME usando a operação de serviço N5g-eir_EquipmentIdentityCheck_Get

## 13. UDM selection
O AMF seleciona um UDM de acordo com o SUPI.

## 14. 14a-14e Nudm_UECM_Registration / Nudm_SDM_Get /Nudm_SDM_Subscribe / Nudm_UECM_DeregistrationNotification / Nsmf_PDUSession_ReleaseSMContext / Nudm_SDM_unsubscribe

## 14a Nudm_UECM_Registration (New AMF -> UDM / UDM -> New AMF)
**Se um novo AMF é um AMF registrado incialmente ou não tem um contexto UE valido**, o novo AMF registra com o UDM usando a operação de serviço *Nudm_UECM_Registration*.

## 14b. Nudm_SDM_Get (New AMF -> UDM / UDM -> New AMF)
obtem dados de inscrição usando *Nudm_SDM_Get*.

## 14c. Nudm_SDM_subscribe (New AMF -> UDM)
O novo AMF inscreve as mudanças de dados de inscrição do UDM usando o **Nudm_SDM_Subscribe**. Assim que acontece mudança dos dados de inscrição, o novo AMF recebe uma notificação do UDM.

## 14d. Nudm_UECM_DeregistrationNotification / Nsmf_PDUSession_ReleaseSMContext (UDM -> Old AMF)
Se o UDM armazena o tipo de acesso do UE e sua associação com o novo AMF, ele incia uma operação de serviço Nudm_UECM_DeregistrationNotification, instruindo o AMF antigo a deletar o contexto UE.

Se a razão para remover o NF indicado pelo NF é *Intial Registration*, O AMF incia a operação de serviço *Nsmf_PDUSession_ReleaseSMCOntext*, notificando o SMF(s) que o UE foi removido do AMF antigo. Apos receber essa notificação, o SMF remove a sessão PDU.

## 14e. Nudm_SDM_unsubscribe (Old AMF -> UDM)
O AMF antigo inicia a operação de serviço *Nudm_SDM_unsubscribe* para remover dados de inscrição no UDM.

## 15. PCF selection
Quando o novo AMF decide estabelecer uma politica de relacionamento como o PCF:
- **Se o novo AMF não tem acesso valido e politica de mobilidade**, o novo AMF seleciona um PCF.
- **Se o novo AMF obtem o ID do PCF do AMF antigo**, o novo AMF pode ver e selecionar diretamento o PCF.

O AMF novo seleciona um PCF novo por meio do NR **Se o ID PCF não poder ser obtido ou não conseguir achar e selecionar o PCF**

## 16. Npcf_AMPolicyControl_Create Request (New AMF -> PCF)
Depos de selecionar um PCF, o AMF novo envia uma mensagem de *Npcf_AMPolicyControl_Create Request* para o PCF, solicitando estabelecer associação de controle de politicas de gerenciamento de acesso (AM - Access Management). Essa mensagem contem **SUPI, notificationUri, suppFeat, etc.**

## 17. Npcf_AMPolicyControl_Create Response (PCF - New AMF)
O PCF determina as politicas baseadas nos dados de inscrição e informações reportadas do AMF, criando uma associação de politicas AM, e responde ao AMF com uma mensagem *Npcf_AMPolicyControl_Create Response*.

## 18. Npcf_AMPolicyControl_Delete Request (Old AMF -> PCF)
**Se o AMF antigo tiver inciado uma associação de politica para o PCF,** este AMF envia um *Npcf_AMPolicyControl_Delete Request* para o PCF, pedindo para deletar a associação com o AMF.

## 19. Npcf_AMPolicyControl_Delete Response (PCF -> Old AMF)
O PCF responde o AMF como um *Npcf_AMPolicyControl_Delete Response*, confirmando ao que a associação de politica de controle do AM foi deletada.

## 20. Registration Accept (New AMF -> UE)
O AMF novo envia uma mensagem de *Registration Accept* para o UE, informando a solicitação de registro foi aceita. Essa mensagem contem o 5G-GUTI que foi alocado, lita TA,etc.

## 21. Npcf_UEPolicyControl_Create Request (New AMF -> PCF)
O AMF novo envia uma mensagem de *Npcf_UEPolicyControl_Create Request* para o PCF, pedindo para estabelecer uma associação de politica do UE. Essa mensagem contem o *SUPI, notificationUri, suppFeat,* etc.

## 22. Npc_UEPolicyControl_Create Response (PCF -> New AMF)
O PCF determina as politicas de acordo com a informação reportada no AMF e dados de inscrição, associação de politica UE, e responde ao AMF com uma mensagem de *Npcf_UEPolicyControl_Create Response*.

## 23. Registration Complete (UE -> New AMF)
Se o AMF aloca um novo 5G-GUTI para o UE durante o procedimento de registration, o UE envia uma mensagem de *Registration Complete ao AMF*.
