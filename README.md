# Templates
Methode de création d'un objet dans une base de donnée
@Override
	public void insert(Contact contact) {
		KeyHolder keyHolder = new GeneratedKeyHolder();
		
		MapSqlParameterSource namedParameters = new MapSqlParameterSource();
		namedParameters.addValue("nom", contact.getNom());
		namedParameters.addValue("prenom", contact.getPrenom());

		jdbcTemplate.update("INSERT INTO CONTACT (nom, prenom) VALUES (:nom,:prenom)", namedParameters, keyHolder);

		if (keyHolder != null && keyHolder.getKey() != null) {
			contact.setIdContact(keyHolder.getKey().intValue());
		}
		
		// TODO : a supprimer
		System.out.println("insertion de "+contact);
	}
