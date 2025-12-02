# hospital-bd-sql
-- Criação do banco de dados
DROP DATABASE IF EXISTS hospital;
CREATE DATABASE hospital;
USE hospital;

-- Tabela CONVENIO (opcional mas recomendada pela normalização)
CREATE TABLE convenio (
    id_convenio INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100),
    telefone VARCHAR(20),
    cobertura VARCHAR(200)
);

-- Tabela PACIENTE
CREATE TABLE paciente (
    id_paciente INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    cpf CHAR(11) NOT NULL UNIQUE,
    data_nascimento DATE,
    telefone VARCHAR(20),
    endereco VARCHAR(200),
    id_convenio INT,
    FOREIGN KEY (id_convenio) REFERENCES convenio(id_convenio)
);

-- Tabela MEDICO
CREATE TABLE medico (
    id_medico INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    crm VARCHAR(20) NOT NULL UNIQUE,
    especialidade VARCHAR(80),
    horario_atendimento VARCHAR(100)
);

-- Tabela CONSULTA
CREATE TABLE consulta (
    id_consulta INT PRIMARY KEY AUTO_INCREMENT,
    data_consulta DATE NOT NULL,
    horario TIME,
    diagnostico TEXT,
    receita TEXT,
    id_paciente INT NOT NULL,
    id_medico INT NOT NULL,
    FOREIGN KEY (id_paciente) REFERENCES paciente(id_paciente),
    FOREIGN KEY (id_medico) REFERENCES medico(id_medico)
);

-- Tabela INTERNACAO
CREATE TABLE internacao (
    id_internacao INT PRIMARY KEY AUTO_INCREMENT,
    quarto VARCHAR(10),
    data_entrada DATE,
    data_saida DATE,
    id_paciente INT NOT NULL,
    id_medico INT NOT NULL,
    FOREIGN KEY (id_paciente) REFERENCES paciente(id_paciente),
    FOREIGN KEY (id_medico) REFERENCES medico(id_medico)
);

-- Tabela EXAME
CREATE TABLE exame (
    id_exame INT PRIMARY KEY AUTO_INCREMENT,
    tipo_exame VARCHAR(100),
    data_exame DATE,
    resultado TEXT,
    id_paciente INT NOT NULL,
    id_medico INT NOT NULL,
    FOREIGN KEY (id_paciente) REFERENCES paciente(id_paciente),
    FOREIGN KEY (id_medico) REFERENCES medico(id_medico)
);
USE hospital;

-- CONVENIOS
INSERT INTO convenio (nome, telefone, cobertura)
VALUES
('Saúde Plus', '41999996666', 'Consultas e exames básicos'),
('Vida Total', '41990005522', 'Cobertura completa'),
('Bem Saúde', '41998887755', 'Apenas consultas');

-- PACIENTES
INSERT INTO paciente (nome, cpf, data_nascimento, telefone, endereco, id_convenio)
VALUES
('Ana Silva', '12345678901', '1990-05-10', '41988887777', 'Rua das Flores, 120', 1),
('Carlos Souza', '98765432100', '1985-02-18', '41997776655', 'Av. Brasil, 500', 2),
('Mariana Costa', '45678912300', '2001-11-25', '41991234567', 'Rua Central, 45', NULL);

-- MEDICOS
INSERT INTO medico (nome, crm, especialidade, horario_atendimento)
VALUES
('Dr. João Almeida', 'CRM1234', 'Cardiologia', '08:00-12:00'),
('Dra. Paula Mendes', 'CRM5678', 'Pediatria', '13:00-17:00'),
('Dr. Lucas Ferraz', 'CRM9101', 'Ortopedia', '09:00-14:00');

-- CONSULTAS
INSERT INTO consulta (data_consulta, horario, diagnostico, receita, id_paciente, id_medico)
VALUES
('2025-03-01', '10:00', 'Gripe', 'Dipirona 500mg', 1, 2),
('2025-03-02', '09:00', 'Dor no peito', 'Encaminhamento para exame', 2, 1),
('2025-03-03', '14:00', 'Luxação no ombro', 'Imobilização', 3, 3);

-- INTERNACOES
INSERT INTO internacao (quarto, data_entrada, data_saida, id_paciente, id_medico)
VALUES
('101A', '2025-03-01', '2025-03-05', 2, 1),
('202B', '2025-03-02', NULL, 1, 2);

-- EXAMES
INSERT INTO exame (tipo_exame, data_exame, resultado, id_paciente, id_medico)
VALUES
('Raio-x', '2025-03-03', 'Fratura leve detectada', 3, 3),
('Hemograma', '2025-03-02', 'Alterações leves', 1, 1),
('Ultrassom', '2025-03-01', 'Normal', 2, 2);
USE hospital;

-- 1. Consultar consultas com paciente e médico
SELECT c.id_consulta, p.nome AS paciente, m.nome AS medico, c.data_consulta
FROM consulta c
JOIN paciente p ON c.id_paciente = p.id_paciente
JOIN medico m ON c.id_medico = m.id_medico
ORDER BY c.data_consulta;

-- 2. Listar pacientes sem convênio
SELECT nome, telefone 
FROM paciente 
WHERE id_convenio IS NULL;

-- 3. Médicos por especialidade
SELECT nome, especialidade
FROM medico
WHERE especialidade = 'Cardiologia';

-- 4. Exames com resultado contendo palavra-chave
SELECT tipo_exame, resultado 
FROM exame
WHERE resultado LIKE '%leve%';

-- 5. Limitar resultados
SELECT nome, cpf 
FROM paciente
ORDER BY nome
LIMIT 2;
USE hospital;

-- UPDATES
UPDATE paciente
SET telefone = '41977776666'
WHERE id_paciente = 1;

UPDATE medico
SET especialidade = 'Clínico Geral'
WHERE id_medico = 3;

UPDATE exame
SET resultado = 'Paciente estável'
WHERE id_exame = 2;

-- DELETES
DELETE FROM exame
WHERE id_exame = 3;

DELETE FROM consulta
WHERE id_consulta = 1;

DELETE FROM internacao
WHERE id_internacao = 1;

